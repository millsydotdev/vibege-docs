# Production Sprint 3.3 Handoff — Professional Runtime & Launcher Experience

## 1. Runtime Architecture Improvements

### Library Scene
- Game detail lines now show `play_time` (formatted as "1h 30m"), `last_played` (formatted as "3d ago"), `play_count`, and `install_size`
- Favourite star (★) shown inline with game name
- Update indicator shown inline (no longer a separate badge)
- Added helper functions: `format_duration()` and `format_days_ago()`

### Download Queue
- `DownloadTask` extended with `speed_bytes_per_sec`, `eta_secs`, `last_update`
- New `DownloadQueue::update_progress(downloaded, total)` calculates transfer speed over a 0.5s sliding window
- ETA computed from `remaining_bytes / speed_bytes_per_sec`
- All existing `enqueue()` calls updated to initialize new fields

## 2. Library Experience Improvements

| Feature | Before | After |
|---------|--------|-------|
| Game detail | v1 by author, size, plays | + play time, last played, formatted durations |
| Favourites | Separate column | Inline ★ prefix in game name |
| Updates | Offset badge | Inline text indicator with colour |
| Last played | Not shown | "3d ago", "2h ago", "recently" |
| Play time | Raw seconds | "1h 30m", "45m" formatted |

## 3. Download Manager Improvements

| Feature | Before | After |
|---------|--------|-------|
| Transfer speed | Not tracked | bytes/sec (0.5s sliding window) |
| ETA | None | Computed from speed + remaining |
| Progress update | Manual | Centralized `update_progress()` method |

## 4. Tests Added

| Crate | Tests | Status |
|-------|-------|--------|
| vibege-scene | 147 (all existing) | ✅ Pass |

## 5. Performance Impact

- Library rendering: O(n) detail formatting per game — negligible
- Download speed tracking: O(1) — single subtraction + division every 0.5s
- ETA calculation: O(1) — single division

## 6. Build / CI Status

| Area | Status |
|------|--------|
| `cargo fmt --check` | ✅ |
| `cargo clippy --all-targets -- -D warnings` | ✅ |
| `cargo test --workspace` | ✅ |
| `cargo build --workspace` | ✅ |

## 7. Git Commit(s)

| Repository | Commit | Message |
|-----------|--------|---------|
| vibege-runtime | `d27e967` | feat(runtime): Sprint 3.3 — library experience, download manager |

## 8. Updated Runtime Maturity Score

| Subsystem | Before | After | Why |
|-----------|--------|-------|-----|
| Library UX | 5/10 | 7/10 | Play time, last played, better formatting |
| Download Manager | 5/10 | 7/10 | Speed tracking, ETA, progress method |
| Scene Tests | 8/10 | 8/10 | 147 tests, unchanged |
| **Overall Runtime** | **6/10** | **7/10** | |
