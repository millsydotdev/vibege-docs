# Getting Started with VibeGE

## Installation

### 1. Install the CLI

```bash
npm install -g @vibege/cli
```

Or build from source:

```bash
git clone https://github.com/millsydotdev/vibege-cli.git
cd vibege-cli
cargo build --release
./target/release/vibege --version
```

### 2. Create a Project

```bash
vibege new my-first-game --template blank
cd my-first-game
```

### 3. Run Your Game

```bash
vibege dev
```

## Your First Game

Open `src/main.lua` and replace the content with:

```lua
function init()
    print("Hello, VibeGE!")
end

function update(dt)
    if vibege.input.is_key_pressed("escape") then
        -- Game will exit
    end
end

function render()
    vibege.render.clear(0.1, 0.2, 0.3, 1.0)
end
```

## Next Steps

- Read the SDK API Reference
- Try the platformer template: `vibege new my-platformer --template platformer`
- Publish your game: `vibege build && vibege publish`
