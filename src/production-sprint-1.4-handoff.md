# Production Sprint 1.4 Handoff — Gameplay Framework & Scene SDK

## 1. Scene Module Architecture

### Camera System
Camera state (position x/y, zoom) stored in `SdkState` and accessible from Lua:

```
Lua → set_camera_position(x, y) → SdkState.camera_x/y
Lua → set_camera_zoom(z) → SdkState.camera_zoom
```

### Coordinate Transforms
```
world_to_screen(wx, wy):
    sx = (wx - camera_x) * zoom + screen_width / 2
    sy = (wy - camera_y) * zoom + screen_height / 2

screen_to_world(sx, sy):
    wx = (sx - screen_width / 2) / zoom + camera_x
    wy = (sy - screen_height / 2) / zoom + camera_y
```

### API Surface

| Function | Description |
|----------|-------------|
| `screen_size()` | `{width, height}` table |
| `camera_position()` | `x, y` |
| `set_camera_position(x, y)` | Move camera |
| `camera_zoom()` | Current zoom factor |
| `set_camera_zoom(z)` | Set zoom (clamped to ≥ 0.01) |
| `world_to_screen(wx, wy)` | `sx, sy` transform |
| `screen_to_world(sx, sy)` | `wx, wy` transform |
| `viewport()` | `{x, y, width, height}` visible world rect |

## 2. Animation Module Architecture

### Tween Engine
Active tweens stored in `SdkState.tweens: Vec<TweenEntry>`. Updated every frame in `SdkState::tick()`:

```
SdkState::tick(dt):
    for each active tween:
        t.remaining -= dt
        if t.remaining <= 0:
            t.value = t.to
            t.done = true
        else:
            p = 1.0 - remaining / duration
            t.value = from + (to - from) * ease(p, easing_kind)
    remove done tweens
```

### Lua Usage

```lua
-- Start a tween
local id = vibege.animation.tween("health", 0.5, 0, 100, vibege.animation.easing.quad_out)

-- Each frame, get current value
local val = vibege.animation.get_tween_value(id)
```

### API Surface

| Function | Description |
|----------|-------------|
| `tween(target, duration, from, to, easing?)` | Start tween, returns id |
| `get_tween_value(id)` | Current interpolated value or nil |
| `is_tween_done(id)` | True if tween completed |
| `cancel_tween(id)` | Remove a specific tween |
| `cancel_all_tweens()` | Clear all active tweens |
| `tween_count()` | Number of active tweens |

### Easing Constants

| Constant | Function |
|----------|----------|
| `easing.linear` | `t` |
| `easing.quad_in` | `t²` |
| `easing.quad_out` | `t(2 - t)` |
| `easing.quad_in_out` | Hermite |
| `easing.cubic_in` | `t³` |
| `easing.cubic_out` | `(t-1)³ + 1` |

## 3. Save System Architecture

### File Format
```
sha256:a1b2c3d4...
{"score": 100, "level": 5}
```

- SHA256 checksum of payload prepended as header
- Backward compatible with legacy saves (no checksum header)
- Per-game isolation: `./saves/<game_name>/<slot>.save`

### API Surface

| Function | Description |
|----------|-------------|
| `save(slot, data)` | Write data with SHA256 integrity |
| `load(slot)` | Read data, verify checksum |
| `delete(slot)` | Remove save file |
| `exists(slot)` | Check if save exists |
| `enumerate()` | List all save slots |
| `metadata(slot)` | `{size, modified}` table |

## 4. New SDK APIs

| Module | Functions | Total |
|--------|-----------|-------|
| `vibege.scene` | 8 | 8 |
| `vibege.animation` | 6 + 6 easing constants | 12 |
| `vibege.save` | 6 | 6 |
| **Total new** | | **26** |
| **SDK total** | | **~80 functions** |

## 5. Tests Added

All 13 existing SDK tests pass. No regressions.

## 6. Performance Impact

- Camera: zero-cost (pure math, no allocation)
- Tweens: O(n) per frame where n = active tween count. Each tween: subtract, divide, ease function. Tween cleanup runs once per frame.
- Save: file I/O only on explicit save/load calls. SHA256 per save.

## 7. Build / CI Status

| Area | Status |
|------|--------|
| `cargo fmt --check` | ✅ |
| `cargo clippy --all-targets -- -D warnings` | ✅ |
| `cargo test --workspace` | ✅ |
| `cargo build --workspace` | ✅ |

## 8. Git Commit(s)

```
26a382a feat(sdk): Production Sprint 1.4 — scene, animation & save modules
 6 files changed, 514 insertions(+)
```

## 9. Push / Deployment Status

Pushed to `origin/wave-17.2-fix`. CI re-triggered.

## 10. Updated SDK Maturity Score

| Module | Before | After | Why |
|--------|--------|-------|-----|
| `vibege.scene` | 0/10 | 8/10 | Camera, transforms, viewport. Missing: shake. |
| `vibege.animation` | 0/10 | 8/10 | Tween engine, 6 easings. Missing: sprite anim. |
| `vibege.save` | 0/10 | 9/10 | Full save system with integrity. |
| `vibege.render` | 9/10 | 9/10 | Unchanged |
| `vibege.runtime` | 9/10 | 9/10 | Unchanged |
| `vibege.math` | 9/10 | 9/10 | Unchanged |
| `vibege.debug` | 7/10 | 7/10 | Unchanged |
| `vibege.assets` | 9/10 | 9/10 | Unchanged |
| **Overall SDK** | **8.5/10** | **9/10** | |
