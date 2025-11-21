# Smart Title Plugin

Auto-generates meaningful session titles for your OpenCode conversations using AI.

## What It Does

- Watches your conversation and generates short, descriptive titles
- Updates automatically when the session becomes idle (you stop typing)
- Uses OpenCode's unified auth - no API keys needed
- Works with any authenticated AI provider

## Installation

```bash
npm install @tarquinen/opencode-smart-title
```

Add to `~/.config/opencode/opencode.json`:

```json
{
  "plugin": ["@tarquinen/opencode-smart-title"]
}
```

## Configuration

Edit `~/.config/opencode/smart-title.jsonc`:

```jsonc
{
  // Enable or disable the plugin
  "enabled": true,

  // Enable debug logging
  "debug": false,

  // Optional: Use a specific model (otherwise uses smart fallbacks)
  // "model": "anthropic/claude-haiku-4-5",

  // Update title every N idle events (1 = every time you pause)
  "updateThreshold": 1
}
```

## How It Works

**Trigger:** Updates when session goes idle (you stop typing)

**Model Selection:**
1. Try configured model (if specified)
2. Try fallback models from authenticated providers in priority order:
   - openai/gpt-5-mini
   - anthropic/claude-haiku-4-5
   - google/gemini-2.5-flash
   - deepseek/deepseek-chat
   - xai/grok-4-fast
   - alibaba/qwen3-coder-flash
   - zai/glm-4.5-flash
   - opencode/big-pickle

**Update Threshold:**
- `1` = Update every idle (default)
- `2` = Update every other idle
- `3` = Update every third idle, etc.

## Troubleshooting

**Enable debug logging:**
```jsonc
{ "debug": true }
```

**View logs:**
```bash
tail -f ~/.config/opencode/logs/smart-title/$(date +%Y-%m-%d).log
```

**Plugin not working?**
- Make sure you're authenticated with at least one provider
- Check that `enabled: true` in config
- Restart OpenCode after config changes

## License

MIT
