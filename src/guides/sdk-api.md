# SDK API Reference

## Input API

```lua
-- Keyboard
vibege.input.is_key_down("space")     -- Currently held
vibege.input.is_key_pressed("space")  -- Just pressed this frame

-- Mouse
local x, y = vibege.input.mouse_position()
local dx, dy = vibege.input.mouse_delta()

-- Gamepad
vibege.input.is_gamepad_connected()
vibege.input.is_gamepad_button_down("a")
```

## Render API

```lua
-- Clear the screen
vibege.render.clear(r, g, b, a)

-- Draw a rectangle
vibege.render.draw_rect(x, y, w, h, r, g, b, a)
```

## Storage API

```lua
-- Read and write files
local data = vibege.storage.read_file("save.dat")
vibege.storage.write_file("save.dat", data)
```

## Time API

```lua
local dt = vibege.time.delta_time()  -- Seconds since last frame
local elapsed = vibege.time.elapsed()  -- Total elapsed time
```
