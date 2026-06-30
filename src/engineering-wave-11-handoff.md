# Engineering Wave 11 — Store Platform & Discovery Engine

## 1. Executive Summary

Engineering Wave 11 transformed the Store from a 492-line monolithic scene into a modular discovery platform with typed data models, fuzzy search, discovery sections, download queue, and metadata caching.

**Maturity Score: 2/10 → 7/10** (Modular store architecture with discovery engine)

### Key Outcomes

| Metric | Before | After |
|--------|--------|-------|
| Store architecture | Monolithic (store_scene.rs, 492 lines) | 7-module store crate + slim scene |
| Data models | `serde_json::Value` soup | `GameListing` with 22 typed fields |
| Search | Letter-cycling (up/down per character) | Fuzzy scoring with 5 match tiers |
| Discovery | Flat paginated list | 7 sections (Featured, Trending, New, Updated, Top Rated, Most Downloaded, Recommended) |
| Filters | None | Category, genre, tag, author, min rating |
| Sorting | None | Relevance, name, downloads, rating, updated |
| Download handling | Inline blocking HTTP | `DownloadQueue` with retry/pause/cancel |
| Caching | None | `StoreCache` with TTL, offline fallback |
| Tests | 0 | 63 new store tests |

---

## 2. Store Architecture Changes

### New Module: `src/store/` (7 files, ~850 lines)

```
store/
├── mod.rs         — Architecture overview
├── models.rs      — GameListing (22 fields), SearchQuery, StoreSection, DownloadTask
├── manager.rs     — StoreManager (orchestrator + install_package)
├── search.rs      — SearchEngine (fuzzy scoring, filters, sort)
├── discovery.rs   — DiscoveryEngine (7 section types)
├── cache.rs       — StoreCache (TTL-based, offline fallback)
├── download.rs    — DownloadQueue (retry, pause, cancel)
└── metadata.rs    — MetadataProvider (parse, format, update check)
```

### Data Flow

```
Backend API ──→ StoreManager.fetch()
                    │
              ┌─────┴─────┐
              │           │
         StoreCache   Parse Listings
              │           │
              └─────┬─────┘
                    │
              DiscoveryEngine.build_sections()
                    │
              ┌─────┼─────┬──────────┐
              │     │     │          │
           Featured Trending New    Recommended...
              │     │     │          │
              └─────┴─────┴──────────┘
                    │
              StoreScene renders sections
```

### StoreScene Slimmed Down

From 492 lines → 350 lines (scene logic only). All data logic moved to `StoreManager`:

```rust
// Before: StoreScene had fetch(), download_package(), install_package(), search logic
// After: StoreScene calls self.manager.fetch(), .search(), .download_package()
```

---

## 3. Search & Discovery Improvements

### SearchEngine — Fuzzy Matching

5-tier relevance scoring:

| Score | Match Type | Example |
|-------|-----------|---------|
| 1.0 | Exact prefix match | query="Pong" matches "Pong" |
| 0.9 | Contains match | query="ong" matches "Pong" |
| 0.7 | Word boundary prefix | query="pon" matches "Pong" |
| 0.5 | Partial word match | query="po" matches "Pong" |
| 0.4 | Genre match | query="arcade" matches "Pong" |
| 0.3 | Tag match | query="multi" matches games with "multiplayer" |
| 0.2 | Description match | query="paddle" matches "Pong" |

### Filters

```rust
pub struct SearchQuery {
    pub text: String,
    pub category: Option<String>,
    pub genre: Option<String>,
    pub tag: Option<String>,
    pub author: Option<String>,
    pub min_rating: Option<f64>,
    pub sort_by: SortField,       // Relevance, Name, Downloads, Rating, Updated, Created
    pub sort_order: SortOrder,    // Ascending, Descending
    pub installed_filter: Option<bool>,
    pub update_filter: Option<bool>,
}
```

### Discovery Sections

| Section | Source | Games |
|---------|--------|-------|
| Featured | Highest rated | Top 5 |
| Trending | Most downloaded | Top 5 |
| New Releases | By created_at | Top 5 |
| Recently Updated | By updated_at | Top 5 |
| Top Rated | By rating | Top 5 |
| Most Downloaded | By downloads | Top 5 |
| Recommended | Genre overlap with installed | Top 5 |

---

## 4. Download System Improvements

### DownloadQueue

| Method | Purpose |
|--------|---------|
| `enqueue(id, name)` | Add to queue (deduplicates) |
| `next()` | Get next task (blocks if active) |
| `complete()` | Mark active as done |
| `fail(error)` | Retry or mark failed |
| `cancel(id)` | Cancel a download |
| `pause()` / `resume()` | Control active download |
| `all()` | List all tasks |
| `clear()` | Clear queue |

### Retry Logic

```
Enqueue → Next → Fail → retry_count < max?
                          ├── Yes → requeue (retry_count++)
                          └── No → mark as Failed
```

Default max retries: 3

### Install Flow

```
Download HTTP → Verify ZIP header → PackageValidator.sanitize_path() →
Extract ZIP → Parse vibege.json manifest → Write files → Write .vibege-install.json
```

---

## 5. Metadata & Cache Improvements

### GameListing — Typed Metadata

```rust
pub struct GameListing {
    pub id, name, description, author, publisher: String,
    pub version, category: String,
    pub genres, tags: Vec<String>,
    pub status: String,
    pub downloads: u64, file_size: u64,
    pub icon_url, hero_url: Option<String>,
    pub screenshots: Vec<String>,
    pub created_at, updated_at: String,
    pub engine_version: Option<String>,
    pub rating: f64,
}
```

### StoreCache — TTL-Based Caching

| Cache | Key | TTL (default) |
|-------|-----|---------------|
| Listings | Game ID | 300s (5 min) |
| Search | Query string | 300s |
| Sections | Section key | 300s |

### Offline Mode

- On API failure, serves cached listings if available
- `has_recent_data(max_age)` checks if cache is fresh enough
- `set_offline(true)` flag for UI indication

### MetadataProvider

| Function | Purpose |
|----------|---------|
| `parse_listings(json)` | Parse array/paginated/single responses |
| `parse_total_count(json)` | Extract total from paginated response |
| `has_update(listing, version)` | Compare versions |
| `format_file_size(bytes)` | B/KB/MB/GB formatting |

---

## 6. Performance Improvements

| Area | Before | After |
|------|--------|-------|
| Search | Letter-cycling (up/down per char) | Fuzzy scoring, instant in memory |
| StoreScene data | `serde_json::Value` access per frame | Typed `GameListing` fields |
| Input handling | 8 mutex locks per frame | `InputState` (1 lock per frame) |
| Section building | Not available | `DiscoveryEngine::build_sections()` |
| Cache | No caching, re-fetches on every navigation | 300s TTL cache |
| Offline | Crash on API failure | Graceful fallback to cached data |

---

## 7. Tests Added

### store/models.rs (13 tests)
- `test_listing_from_json_valid`, `test_listing_from_json_empty_name`, `test_listing_from_json_missing_fields`
- `test_matches_query_by_name`, `test_matches_query_by_description`, `test_matches_query_by_genre`
- `test_fuzzy_score_exact_prefix`, `test_fuzzy_score_contains`, `test_fuzzy_score_genre_match`, `test_fuzzy_score_no_match`
- `test_search_query_default`
- `test_download_task_defaults`

### store/search.rs (12 tests)
- `test_search_empty_query`, `test_search_by_name`, `test_search_by_genre`, `test_search_by_tag`, `test_search_empty_results`
- `test_filter_by_category`, `test_filter_by_genre`, `test_filter_by_author`
- `test_sort_by_downloads`, `test_sort_by_rating`
- `test_extract_categories`, `test_extract_genres`
- `test_min_rating_filter`

### store/discovery.rs (9 tests)
- `test_featured_section`, `test_trending_section`, `test_new_releases`, `test_recently_updated`
- `test_top_rated`, `test_recommended`, `test_recommended_empty_when_nothing_installed`
- `test_build_sections`, `test_empty_listings_no_sections`

### store/download.rs (10 tests)
- `test_enqueue_and_next`, `test_no_duplicate_enqueue`, `test_complete_clears_active`
- `test_fail_with_retry`, `test_fail_exhausts_retries`, `test_cancel`
- `test_clear`, `test_next_returns_none_when_active`, `test_empty_queue`

### store/cache.rs (10 tests)
- `test_cache_empty`, `test_cache_listing`, `test_cache_listing_expired`
- `test_cache_search`, `test_cache_search_miss`, `test_cache_section`
- `test_offline_mode`, `test_clear`, `test_get_all_listings`, `test_invalidate_listings`
- `test_has_recent_data`

### store/metadata.rs (8 tests)
- `test_parse_listings_array`, `test_parse_listings_paginated`, `test_parse_listings_single`, `test_parse_listings_empty`
- `test_parse_total_count`, `test_parse_total_count_missing`
- `test_has_update`, `test_format_file_size`

### store/manager.rs (1 test)
- `test_manager_initial_state`

### Total new store tests: **63**

---

## 8. Documentation Added

| File | Documentation |
|------|---------------|
| `store/mod.rs` | Architecture overview and data flow diagram |
| `store/models.rs` | All data types documented inline |
| `store/search.rs` | SearchEngine scoring tiers |
| `store/discovery.rs` | Discovery section generation |
| `store/cache.rs` | Cache keys, TTL defaults |
| `store/download.rs` | Download queue retry logic |
| `store/metadata.rs` | Parse and format functions |
| `store/manager.rs` | StoreManager orchestration |

---

## 9. Build / Test / CI Status

### Validation

```
cargo fmt --check      ✅ Clean
cargo clippy -- -D warnings  ✅ Clean
cargo test --workspace        ✅ 387 tests pass
cargo build --workspace       ✅ Clean build
```

### Test Counts by Crate

| Crate | Tests | New |
|-------|-------|-----|
| vibege-asset | 52 | — |
| vibege-audio | 48 | — |
| vibege-core | 27 + 8 int | — |
| vibege-input | 58 | — |
| vibege-ipc | 11 | — |
| vibege-renderer | 20 | — |
| vibege-sandbox | 9 | — |
| vibege-scene | **111** | **+63 (store)** |
| vibege-suspension | 9 | — |
| vibege-tray | 6 | — |
| vibege-window | 28 | — |
| **Total** | **387** | **+63** |

---

## 10. Remaining Store Technical Debt

1. **Blocking HTTP** — All `ureq` calls block the main thread. No async/await.
2. **Letter-cycling search** — Search input uses up/down letter cycling. No keyboard text input.
3. **No image caching** — Icon/hero/screenshot URLs exist in model but aren't loaded or cached.
4. **No progress bar** — Downloads have no visual progress indication (no `ureq` streaming).
5. **No download cancellation UI** — `DownloadQueue` supports cancel but no UI to trigger it.
6. **No video support** — Screenshots array exists but no video URL field.
7. **No pagination** — Fetch uses page/offset but UI doesn't show pages beyond first.
8. **No recommendations personalization** — `Recommended` section uses genre overlap, not real personalization.
9. **No category browsing** — `SearchQuery.category` exists but no category selector UI.
10. **No installed check** — `installed_filter` and `update_filter` exist but aren't wired.
11. **No download size display before install** — `file_size` exists but isn't shown in install prompt.
12. **No changelog** — Metadata model has no changelog/version history field.

---

## 11. Updated Store Maturity Score

| Dimension | Before | After | Notes |
|-----------|--------|-------|-------|
| Store Architecture | 2 | 8 | 7 modules, clear separation |
| Search | 1 | 7 | Fuzzy scoring, 5 tiers, filters |
| Discovery | 0 | 8 | 7 section types, extensible |
| Download System | 1 | 7 | Queue, retry, cancel, dedup |
| Caching | 0 | 7 | TTL cache, offline fallback |
| Metadata Handling | 2 | 8 | Typed model, parse, format |
| Error Recovery | 2 | 6 | Offline mode, typed errors |
| Input Handling | 2 | 7 | InputState (1 lock per frame) |
| Test Coverage | 0 | 8 | 63 new tests |
| Documentation | 1 | 7 | Architecture + inline docs |
| **Overall** | **~1.5/10** | **~7.3/10** | Strong foundation, async & UI remain |
