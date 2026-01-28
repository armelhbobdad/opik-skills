# Opik Configuration Reference

## Configuration Precedence

Opik configuration is resolved in this order (highest to lowest priority):

1. **Constructor arguments** - Passed directly to Opik client
2. **Environment variables** - `OPIK_*` env vars
3. **Config file** - `~/.opik.config`
4. **Defaults** - Built-in default values

## Config File Format

Location: `~/.opik.config`

```toml
[opik]
api_key = "your-api-key"
url_override = ""
workspace = "your-workspace"
```

The config file is created automatically by `opik configure`.

## Cloud Setup (Opik Cloud)

### Get Your API Key

1. Go to [comet.com](https://www.comet.com)
2. Sign in or create an account
3. Navigate to Settings → API Keys
4. Copy your API key

### Configure

**Option 1: Interactive**
```bash
opik configure
```

> **Note:** `opik configure` will overwrite existing `~/.opik.config`. Back up your config first if you need to preserve it: `cp ~/.opik.config ~/.opik.config.backup`

**Option 2: Environment Variable**
```bash
export OPIK_API_KEY="your-api-key-here"
```

**Option 3: Config File**
```bash
echo '[opik]
api_key = "your-api-key-here"' > ~/.opik.config
```

## Self-Hosted Setup

For self-hosted Opik deployments, use `OPIK_URL_OVERRIDE`:

```bash
export OPIK_URL_OVERRIDE="http://localhost:5173/api"
```

Or in config file:

```toml
[opik]
url_override = "http://your-opik-server:5173/api"
```

No API key is required for self-hosted instances.

## Configuration in Code

### Python

```python
from opik import Opik

# Use environment/config file (recommended)
client = Opik()

# Or pass explicitly
client = Opik(
    api_key="your-api-key",
    workspace="your-workspace",
    project_name="my-project"
)
```

### TypeScript

```typescript
import { Opik } from "opik";

// Use environment/config file
const client = new Opik();

// Or pass explicitly
const client = new Opik({
  apiKey: "your-api-key",
  workspace: "your-workspace",
  projectName: "my-project"
});
```

## Workspace and Project

- **Workspace**: Organizational unit for grouping projects
- **Project**: Container for traces and evaluations

If not specified:
- Workspace defaults to your account's default workspace
- Project defaults to `"default"`

## Troubleshooting

### "Connection failed" on healthcheck

1. **Check API key**: Ensure `OPIK_API_KEY` is set correctly
2. **Check network**: Verify you can reach comet.com
3. **Check URL override**: If self-hosted, verify the URL is correct

```bash
# Debug: print current config
python -c "from opik import Opik; c = Opik(); print(c)"
```

### "Invalid API key" error

- Regenerate API key in Comet dashboard
- Check for trailing whitespace in env var
- Ensure no quotes around the value in shell exports

### Config file not found

The config file is optional. If not present, Opik will use environment variables or defaults.

To create one:
```bash
opik configure
```

### Self-hosted connection issues

1. Verify Opik server is running
2. Check the URL format: `http://host:port/api`
3. Ensure no firewall blocking the connection

```bash
# Test connectivity
curl http://your-opik-server:5173/api/health
```

## Environment Variable Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `OPIK_API_KEY` | API key for Opik Cloud | `abc123...` |
| `OPIK_URL_OVERRIDE` | Custom API URL (self-hosted) | `http://localhost:5173/api` |
| `OPIK_WORKSPACE` | Workspace name | `my-workspace` |
| `OPIK_PROJECT_NAME` | Default project name | `my-project` |
