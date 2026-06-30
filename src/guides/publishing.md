# Publishing Your Game

## Building

```bash
vibege build
```

The build command creates a `.vibepkg` file in the `build/` directory.

## Prerequisites

You need a registry authentication token. Set it via:

```bash
export VIBEGE_TOKEN="your-token-here"
```

Or pass it directly to the publish command:

```bash
vibege publish --token "your-token-here"
```

## Publishing

```bash
vibege publish
```

The publish command:
1. Registers the package in the registry
2. Creates a version entry with SHA256 checksum
3. Uploads the `.vibepkg` file
4. Verifies upload integrity
5. Rolls back the version entry if upload fails

## Version Management

```bash
# Update your game
vibege build
vibege publish
```

The registry URL can be configured via the `VIBEGE_REGISTRY_URL` environment variable (default: `http://localhost:3000/api/v1`).
