# SDK API Reference

## Input API

```lua
-- Keyboard
vibege.input.is_key_down("space")         -- Currently held
vibege.input.is_key_pressed("space")      -- Just pressed this frame
vibege.input.is_key_released("space")     -- Just released this frame
vibege.input.key_state("space")           -- Returns "pressed", "held", "released", or "idle"

-- Mouse
local x, y = vibege.input.mouse_position()
local dx, dy = vibege.input.mouse_delta()
local sx, sy = vibege.input.scroll_delta()
vibege.input.is_mouse_down("left")        -- "left", "right", "middle", "back", "forward"
vibege.input.is_mouse_pressed("left")

-- Gamepad
vibege.input.is_gamepad_connected()
vibege.input.is_gamepad_down("a")         -- "a", "b", "x", "y", "left_trigger", etc.
vibege.input.gamepad_axis("lx")           -- Returns -1.0 to 1.0
```

## Render API

```lua
-- Clear the screen
vibege.render.clear(r, g, b, a)

-- Draw a rectangle
vibege.render.draw_rect(x, y, w, h, r, g, b, a)
```

## Audio API

```lua
-- Play a sound from cached samples
local handle = vibege.audio.play("hit")   -- Returns handle or nil
if handle then
    handle:set_looping(true)              -- Toggle looping
    handle:set_volume(0.5)                -- 0.0 to 1.0
    handle:pause()
    handle:resume()
    handle:stop()
end
```

## Assets API

```lua
-- Query asset cache
vibege.assets.exists("player_sprite")     -- Check if loaded
vibege.assets.release("player_sprite")    -- Release from cache
```

## Storage API

```lua
-- Key-value storage (per game, in-memory)
vibege.storage.save("score", "42")        -- Save a value
local score = vibege.storage.load("score") -- Load a value (returns nil if missing)
vibege.storage.delete("score")            -- Delete a key
local keys = vibege.storage.keys()        -- Get all keys (sorted table)
```

## Runtime API

```lua
local version = vibege.runtime.engine_version()  -- e.g. "0.2.0-alpha.1"
local width, height = vibege.runtime.screen_size()
local platform = vibege.runtime.platform()        -- e.g. "windows", "linux", "macos"
```

## Utility API

```lua
-- Logging (appears in runtime logs)
vibege.util.log("Hello from my game!")

-- Random numbers
local r = vibege.util.random(0.0, 1.0)         -- Float in range
local n = vibege.util.random_int(1, 6)          -- Integer in range (inclusive)

-- Seeded mode (for replays / deterministic games)
vibege.util.set_seed(42)

-- Math utilities
local clamped = vibege.util.clamp(value, min, max)
local interpolated = vibege.util.lerp(a, b, t)  -- Linear interpolation
```
