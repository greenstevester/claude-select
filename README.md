# Claude Select

A unified launcher for Claude Code that lets you interactively choose which LLM backend to use.

```
   ____  _                 _        ____       _           _
  / ___|| | __ _ _   _  __| | ___  / ___|  ___| | ___  ___| |_
 | |    | |/ _` | | | |/ _` |/ _ \ \___ \ / _ \ |/ _ \/ __| __|
 | |___ | | (_| | |_| | (_| |  __/  ___) |  __/ |  __/ (__| |_
  \____||_|\__,_|\__,_|\__,_|\___| |____/ \___|_|\___|\___|\__|
                                                      from Bessi
```

## What is this?

Claude Select allows you to run [Claude Code](https://github.com/anthropics/claude-code) with different LLM backends. It supports:

- **Direct API providers** (Kimi, GLM, etc.)
- **Local proxies** via [vibeproxy](https://github.com/automazeio/vibeproxy) (Qwen, Gemini, etc.)

Each model gets its own isolated config directory, so your settings and history remain separate per backend.

## Requirements

- [Claude Code](https://github.com/anthropics/claude-code) installed and available in PATH
- [vibeproxy](https://github.com/automazeio/vibeproxy) running on `localhost:8317` (for proxy-based models)
- zsh shell

## Setup

Make the script executable:

```bash
chmod +x claude-select.sh
```

## Usage

### Interactive Mode

```bash
./claude-select.sh
```

Shows a menu to select your model:

```
  Select a model:

  1) GLM 4.6
  2) Kimi K2 Thinking
  3) Kimi K2 Thinking (alt key)
  4) Qwen3 Coder Plus
  5) Gemini 3 Pro Preview

Enter choice [1-5]:
```

### Direct Mode

Pass the model number as the first argument:

```bash
./claude-select.sh 1              # Launch with GLM 4.6
./claude-select.sh 4              # Launch with Qwen3
```

### With Claude Arguments

Any additional arguments are passed to Claude:

```bash
./claude-select.sh 2 --resume     # Kimi with resume flag
./claude-select.sh 5 -p "hello"   # Gemini with prompt
```

## Adding a New Model

Edit `claude-select.sh` and add a new entry. Find the configuration section and add your model:

### For vibeproxy-based models

If you're using [vibeproxy](https://github.com/automazeio/vibeproxy) to proxy a model:

```bash
MODELS[6]="your-model-name"
BASE_URLS[6]="http://localhost:8317"
AUTH_TOKENS[6]="factory-api-key"
CONFIG_DIRS[6]="${HOME}/.your-model-config"
```

Make sure the model is configured in your vibeproxy setup.

### For direct API models

For providers with their own API endpoints:

```bash
MODELS[6]="model-name"
BASE_URLS[6]="https://api.provider.com/endpoint"
AUTH_TOKENS[6]="your-api-key"
CONFIG_DIRS[6]="${HOME}/.model-config"
```

Then update the menu in `show_menu()`:

```bash
echo "  6) Your Model Name"
```

And update the validation regex from `^[1-5]$` to `^[1-6]$` in both places.

## How It Works

1. Creates a temporary fake `security` executable to bypass macOS Keychain (forces Claude to use config.json)
2. Sets environment variables for the selected backend:
   - `ANTHROPIC_BASE_URL` - API endpoint
   - `ANTHROPIC_AUTH_TOKEN` - API key
   - `ANTHROPIC_DEFAULT_SONNET_MODEL` - Model identifier
   - `ANTHROPIC_DEFAULT_OPUS_MODEL` - Model identifier
   - `CLAUDE_CONFIG_DIR` - Isolated config directory
3. Launches Claude Code with the configured environment
4. Cleans up temporary files on exit

## License

MIT
