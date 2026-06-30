# VibeGE Skill Alignment Report — Canonical Revision

## 1. Executive Summary

This report maps every VibeGE repository and subsystem to its correct existing AI Skill(s). It identifies duplicates among the 1,785 available skills, establishes canonical Skills for each engineering domain, defines reusable Skill Bundles, and prescribes the skill loading workflow for all future Engineering Waves.

**Key outcomes:**

- 21 engineering domains mapped to canonical Skills
- 7 Skill Bundles defined (reduced from 12)
- 6 duplicate/redundant Skill groups identified
- Engineering Waves 1–13 mapped to recommended bundles
- Future workflow codified

---

## 2. Repository Alignment

Every VibeGE repository is assigned a primary Skill, supporting Skills, and a status classification.

| Repository | Primary Language | Canonical Skill | Supporting Skills | Status |
|---|---|---|---|---|
| `vibege-runtime` | Rust | `rust-engineer` | `test-master`, `architecture`, `performance-optimizer` | Fully Aligned |
| `vibege-cli` | Rust | `rust-engineer` | `cli-developer` | Fully Aligned |
| `vibege-backend` | TypeScript | `hono` | `supabase`, `auth-implementation-patterns`, `api-endpoint-builder`, `test-master` | Fully Aligned |
| `vibege-web` | TypeScript/React | `nextjs-developer` | `react-expert`, `frontend-api-integration-patterns` | Fully Aligned |
| `vibege-docs` | Markdown | `api-documentation` | — | Mostly Aligned |
| `vibege-specs` | Markdown | `architecture-designer` | — | Mostly Aligned |
| `vibege-games` | Lua | `rust-engineer` | `2d-games`, `game-design` | Mostly Aligned |

### Subsystem Breakdown

| Subsystem | Module | Canonical Skill | Supporting Skills | Status |
|---|---|---|---|---|
| Core | vibege-core | `rust-engineer` | `architecture` | Fully Aligned |
| Windowing | vibege-window | `rust-engineer` | — | Fully Aligned |
| Renderer | vibege-renderer | `rust-engineer` | `2d-games` | Fully Aligned |
| Audio | vibege-audio | `rust-engineer` | `game-audio` | Fully Aligned |
| Input | vibege-input | `rust-engineer` | — | Fully Aligned |
| IPC | vibege-ipc | `rust-engineer` | — | Fully Aligned |
| Sandbox | vibege-sandbox | `rust-engineer` | `security-reviewer` | Fully Aligned |
| Suspension | vibege-suspension | `rust-engineer` | — | Fully Aligned |
| Tray | vibege-tray | `rust-engineer` | — | Fully Aligned |
| Config | vibege-config | `rust-engineer` | — | Fully Aligned |
| SDK | vibege-sdk | `rust-engineer` | `2d-games`, `game-design` | Fully Aligned |
| Scene Manager | vibege-scene | `rust-engineer` | — | Fully Aligned |
| Asset | vibege-asset | `rust-engineer` | — | Fully Aligned |
| Store | scene/store/ | `rust-engineer` | `api-endpoint-builder` | Mostly Aligned |
| Library | scene/library/ | `rust-engineer` | — | Fully Aligned |
| Game Runtime | scene/runtime/ | `rust-engineer` | `game-development` | Mostly Aligned |
| Lua Bindings | vibege-sdk/src/ | `rust-engineer` | `2d-games`, `game-design` | Mostly Aligned |
| Backend API | vibege-backend | `hono` | `supabase`, `auth-implementation-patterns`, `api-endpoint-builder` | Fully Aligned |
| Web Frontend | vibege-web | `nextjs-developer` | `react-expert`, `frontend-api-integration-patterns` | Fully Aligned |
| CLI | vibege-cli | `rust-engineer` | `cli-developer` | Fully Aligned |
| CI/CD | .github/ | `github-actions-advanced` | — | Fully Aligned |

---

## 3. Canonical Skill Catalogue

Every engineering domain used by VibeGE has a single **canonical** (primary) Skill. Supporting Skills are listed for secondary needs.

| Domain | Canonical Skill | Supporting Skills | Rationale |
|---|---|---|---|
| Rust systems programming | `rust-engineer` | `performance-optimizer`, `architecture` | Covers ownership, lifetimes, async, error handling |
| TypeScript type safety | `typescript-expert` | — | Most comprehensive TS coverage |
| Hono REST API | `hono` | `api-endpoint-builder`, `supabase` | Framework-specific; backend uses Hono |
| Next.js web app | `nextjs-developer` | `react-expert`, `frontend-api-integration-patterns` | Framework-specific; web uses Next.js |
| 2D game development | `2d-games` | `game-design`, `game-audio` | Sprites, tilemaps, physics, camera |
| GPU programming | `rust-engineer` | — | Covers wgpu usage via general Rust patterns |
| Desktop windowing | `rust-engineer` | — | Covers winit/windows-sys usage |
| CLI tooling | `cli-developer` | — | Argument parsing, progress, completion scripts |
| CI/CD pipeline | `github-actions-advanced` | — | Reusable workflows, matrix builds, caching |
| Testing | `test-master` | `tdd` | Covers all test types across all languages |
| Security review | `security-reviewer` | `backend-security-coder` | SAST, OWASP, vulnerability patterns |
| API documentation | `api-documentation` | — | OpenAPI specs, developer guides |
| Architecture | `architecture` | `architecture-designer`, `architecture-review` | ADRs, trade-off analysis, stress-testing |
| Cloud infrastructure | `terraform-engineer` | `docker-expert` | Terraform + Docker for backend deployment |

---

## 4. Duplicate & Redundant Skills

| Group | Skills | Classification | Notes |
|---|---|---|---|
| Rust | `rust`, `rust-engineer`, `rust-pro` | **Canonical**: `rust-engineer` — **Redundant**: `rust`, `rust-pro` | `rust-engineer` is the most comprehensive; `rust` is too generic; `rust-pro` overlaps heavily |
| TypeScript | `typescript`, `typescript-expert`, `typescript-pro` | **Canonical**: `typescript-expert` — **Redundant**: `typescript`, `typescript-pro` | `typescript-expert` covers type-level programming, performance, migration |
| REST APIs | `api-endpoint-builder`, `api-design-principles`, `api-patterns` | **Canonical**: `api-endpoint-builder` — **Supporting**: `api-design-principles` — **Deprecated**: `api-patterns` | `api-endpoint-builder` is the most actionable for endpoint creation |
| Secure coding | `security-reviewer`, `secure-code-guardian`, `backend-security-coder` | **Canonical**: `security-reviewer` — **Supporting**: `backend-security-coder` — **Redundant**: `secure-code-guardian` | `security-reviewer` produces structured audit reports; `secure-code-guardian` is a generic subset |
| Testing | `test-master`, `testing-patterns`, `tdd` | **Canonical**: `test-master` — **Supporting**: `tdd` — **Redundant**: `testing-patterns` | `test-master` is the most comprehensive across all test types |
| Documentation | `documentation`, `api-documentation`, `docs-architect` | **Canonical**: `api-documentation` — **Supporting**: `docs-architect` — **Redundant**: `documentation` | `api-documentation` is most relevant; `docs-architect` for long-form engineering reports |
| Game dev orchestration | `game-development`, `2d-games`, `game-design` | **Canonical**: `2d-games` — **Supporting**: `game-design` — **Deprecated**: `game-development` | `2d-games` covers the actual rendering/physics work; `game-development` is a generic orchestrator with less specific guidance |

---

## 5. Skill Bundle Catalogue

### Bundle 1: Rust Engine Development
```
rust-engineer, architecture, test-master
```
- **Purpose**: Build and maintain the Rust runtime crates
- **When to load**: Any wave modifying `crates/vibege-*`
- **Canonical**: `rust-engineer`
- **Supporting**: `architecture` (design decisions), `test-master` (unit/integration tests)
- **Used by Waves**: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13

### Bundle 2: Game SDK Development
```
rust-engineer, 2d-games, game-design, test-master
```
- **Purpose**: Build and expand the Lua SDK and game-facing API
- **When to load**: Modifying `crates/vibege-sdk`
- **Canonical**: `rust-engineer`
- **Supporting**: `2d-games` (sprite/texture API design), `game-design` (game API ergonomics), `test-master` (SDK tests)
- **Used by Waves**: 13

### Bundle 3: Backend API Development
```
hono, supabase, auth-implementation-patterns, api-endpoint-builder, typescript-expert, test-master
```
- **Purpose**: Build and maintain the Hono REST API
- **When to load**: Any wave modifying `vibege-backend/`
- **Canonical**: `hono`
- **Supporting**: `supabase` (database), `auth-implementation-patterns` (JWT/password flow), `api-endpoint-builder` (route structure), `typescript-expert` (type safety), `test-master` (API tests)
- **Used by Waves**: (none yet — future)

### Bundle 4: Web Frontend Development
```
nextjs-developer, react-expert, typescript-expert, frontend-api-integration-patterns
```
- **Purpose**: Build and maintain the Next.js web app
- **When to load**: Any wave modifying `vibege-web/`
- **Canonical**: `nextjs-developer`
- **Supporting**: `react-expert` (component architecture), `typescript-expert` (type safety), `frontend-api-integration-patterns` (API client patterns)
- **Used by Waves**: (none yet — future)

### Bundle 5: CLI Development
```
rust-engineer, cli-developer
```
- **Purpose**: Build and maintain the `vibege` CLI
- **When to load**: Any wave modifying `vibege-cli/`
- **Canonical**: `rust-engineer`
- **Supporting**: `cli-developer` (argument parsing, progress, completions)
- **Used by Waves**: 2 (initial CLI)

### Bundle 6: DevOps & CI/CD
```
github-actions-advanced, terraform-engineer, docker-expert
```
- **Purpose**: CI/CD pipelines, deployment, infrastructure
- **When to load**: Setting up or modifying CI/CD, deployment config, Docker/Terraform
- **Canonical**: `github-actions-advanced`
- **Supporting**: `terraform-engineer` (IaC), `docker-expert` (containerization)
- **Used by Waves**: (all waves via CI, none explicitly)

### Bundle 7: Quality & Review
```
test-master, security-reviewer, architecture-review
```
- **Purpose**: Validate work before completion
- **When to load**: During the validation phase of every wave
- **Canonical**: `test-master`
- **Supporting**: `security-reviewer` (vulnerability audit), `architecture-review` (design stress-test)
- **Used by Waves**: (should be loaded in every wave)

---

## 6. Engineering Wave → Skill Bundle Mapping

| Wave | Description | Bundle That Should Have Been Used | Primary Skill | Supporting Skills |
|---|---|---|---|---|
| 1 | Initial setup | Bundle 1 (Rust Engine) | `rust-engineer` | `architecture` |
| 2 | Core & CLI | Bundle 1 + Bundle 5 (CLI) | `rust-engineer` | `cli-developer` |
| 3 | Input system | Bundle 1 (Rust Engine) | `rust-engineer` | `test-master` |
| 4 | Renderer | Bundle 1 (Rust Engine) | `rust-engineer` | `2d-games`, `performance-optimizer` |
| 5 | Audio & IPC | Bundle 1 (Rust Engine) | `rust-engineer` | `game-audio` |
| 6 | Scene System | Bundle 1 (Rust Engine) | `rust-engineer` | `architecture` |
| 7 | Config System | Bundle 1 (Rust Engine) | `rust-engineer` | — |
| 8 | Overlay & Window | Bundle 1 (Rust Engine) | `rust-engineer` | — |
| 9 | Asset System | Bundle 1 (Rust Engine) | `rust-engineer` | `test-master` |
| 10 | Package Runtime | Bundle 1 (Rust Engine) | `rust-engineer` | `architecture`, `test-master` |
| 11 | Store Platform | Bundle 1 (Rust Engine) | `rust-engineer` | `api-endpoint-builder`, `test-master` |
| 12 | Library Platform | Bundle 1 (Rust Engine) | `rust-engineer` | `test-master` |
| 13 | SDK & Game API | Bundle 1 + Bundle 2 (Game SDK) | `rust-engineer` | `2d-games`, `game-design`, `test-master` |

**Observation**: All 13 waves required Bundle 1 (Rust Engine) because all work was in the Rust runtime. No wave targeted the backend, web frontend, or required DevOps infrastructure changes. Future waves that target `vibege-backend` should use Bundle 3, waves targeting `vibege-web` should use Bundle 4.

---

## 7. Future Engineering Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│                  EVERY ENGINEERING WAVE                           │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│   Step 1 — Identify Target Subsystem                              │
│   ┌───────────────────────────────────────────────────────────┐   │
│   │ vebege-runtime/* → Bundle 1 (Rust Engine)                │   │
│   │ vibege-sdk/*      → Bundle 2 (Game SDK)                  │   │
│   │ vibege-backend/*  → Bundle 3 (Backend API)               │   │
│   │ vibege-web/*      → Bundle 4 (Web Frontend)              │   │
│   │ vibege-cli/*      → Bundle 5 (CLI)                       │   │
│   │ .github/*         → Bundle 6 (DevOps)                    │   │
│   └───────────────────────────────────────────────────────────┘   │
│                                                                   │
│   Step 2 — Load Skill Bundle                                      │
│   Use the skill tool to activate the bundle's canonical skill.    │
│   Loading the canonical skill loads its intent and guidance.      │
│                                                                   │
│   Step 3 — Verify Skills Active                                   │
│   Confirm the correct skills are loaded before writing code.      │
│                                                                   │
│   Step 4 — Implement                                               │
│   Execute the work using the loaded skills as guidance.           │
│                                                                   │
│   Step 5 — Validate                                                │
│   Load Bundle 7 (Quality & Review) and run:                       │
│     - cargo test --workspace                                     │
│     - cargo clippy --all-targets -- -D warnings                  │
│     - cargo fmt --check                                           │
│     - (for backend/web) npm test && npm run typecheck             │
│                                                                   │
│   Step 6 — Handoff                                                │
│   Generate Engineering Wave handoff document.                     │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

### Skill Loading by Work Type

| If the wave targets... | Load Bundle | Canonical Skill | Then run |
|---|---|---|---|
| Rust crate (runtime) | Bundle 1 | `rust-engineer` | `cargo test --workspace` |
| SDK bindings | Bundle 2 | `rust-engineer` | `cargo test -p vibege-sdk` |
| Backend API | Bundle 3 | `hono` | `npm test && npm run typecheck` |
| Web frontend | Bundle 4 | `nextjs-developer` | `npm run build && npm run typecheck` |
| CLI | Bundle 5 | `rust-engineer` | `cargo test -p vibege-cli` |
| CI/CD infra | Bundle 6 | `github-actions-advanced` | (manual check) |
| Validation (every wave) | Bundle 7 | `test-master` | See Step 5 above |

---

## 8. AGENTS.md Alignment

The `AGENTS.md` file at the repository root has been updated to:

1. Remove the outdated **Current State** section (reflected 77 tests — now 455+)
2. Remove the **SDK Deleted** section (SDK was recreated in Wave 13)
3. Add the **Skill Alignment** section with the bundle table and Pre-Wave Checklist
4. Add the **Recommended Bundles** table matching Bundle Catalogue (Section 5)

All changes are in the committed `AGENTS.md`.

---

## 9. Final Recommendations

### What to stop doing
- Stop loading `rust`, `typescript`, `testing-patterns`, `secure-code-guardian`, `documentation`, `api-patterns` — they are redundant
- Stop starting Engineering Waves without loading a Skill Bundle

### What to start doing
- Load the correct Bundle (Section 5) before every wave
- Load Bundle 7 (Quality & Review) in the validation phase of every wave
- Use `test-master` as the canonical testing skill (not `tdd` alone)

### What we cannot do (and should not try)
- There is no skill for Lua/mlua FFI patterns, wgpu/WebGPU, or desktop windowing (winit)
- These domains are covered by `rust-engineer` with framework-specific documentation
- Do NOT create new skills to fill these gaps — the existing skills are sufficient
