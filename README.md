# Claude Code with Fireworks.ai API

A lightweight proxy to use Fireworks.ai with Claude Code without the 5K token streaming limitation. 

## Features

- Handle automatically streaming and non-streaming modes based on Claude-Code request.
- Tool/function calling support
- Pre-defined Claude Code user/system prompt replacement feature

## Prerequisites

- Node.js 18 or higher
- A Fireworks.ai API key
- npm or yarn

## Installation

### Quick Install (Recommended)

1. Install Claude-code if it not installed yet:
```bash
npm install -g @anthropic-ai/claude-code
```

2. Install claude-code-fireworks:
```bash
npm install -g claude-code-fireworks
```

3. Run ccf once to create the configuration directory:
```bash
ccf
```

This will create `~/.claude-code-fireworks/` with:
- `.env` - Configuration file (edit this to add your API key)
- `replace/` - Directory for prompt replacement JSON files
- `logs/` - Directory for request/response logs

4. Edit the configuration file and add your Fireworks API key:
```bash
# On Linux/Mac
nano ~/.claude-code-fireworks/.env

# On Windows
notepad %USERPROFILE%\.claude-code-fireworks\.env
```

## Usage

1. Start the proxy:
```bash
ccf
```

2. In another terminal session, start Claude Code with proxy settings:
```bash
export ANTHROPIC_BASE_URL=http://127.0.0.1:3000
export ANTHROPIC_AUTH_TOKEN=ANYTHING
export API_TIMEOUT_MS=600000

claude
```

## Configuration

### Configuration Directory

All configuration files are stored in `~/.claude-code-fireworks/`:
- **`.env`** - Main configuration file
- **`replace/`** - Prompt replacement JSON files
- **`logs/`** - Request/response logs

### Environment Variables

Edit `~/.claude-code-fireworks/.env` to configure:

```env
LISTEN_HOST=127.0.0.1
LISTEN_PORT=3000

FIREWORKS_BASE=https://api.fireworks.ai/inference/v1
FIREWORKS_API_KEY=your_api_key_here
FIREWORKS_MODEL=accounts/fireworks/models/glm-4p5
```

- `FIREWORKS_API_KEY` - Your Fireworks.ai API key (required)
- `FIREWORKS_MODEL` - The Fireworks model to use (default: accounts/fireworks/models/glm-4p5)
- `LISTEN_HOST` - Host to bind the server to (default: 127.0.0.1)
- `LISTEN_PORT` - Port to listen on (default: 3000)
- `FIREWORKS_BASE` - Fireworks API base URL (default: https://api.fireworks.ai/inference/v1)

### Message Replacements

You can replace the pre-defined Claude prompt by adding JSON files to `~/.claude-code-fireworks/replace/`:

```json
{
  "original_message": "Text to find",
  "updated_message": "Text to replace with"
}
```

The proxy will automatically load all `.json` files from `~/.claude-code-fireworks/replace/` on startup and apply exact string matches to message content. 

You must log output and input from logs to use the exact message. Here an example of replacement of the prompt triggered when execute the /init command in Claude Code (save as `~/.claude-code-fireworks/replace/init-prompt.json`):

```json
{
  "original_message": "Please analyze this codebase and create a CLAUDE.md file, which will be given to future instances of Claude Code to operate in this repository.\n\nWhat to add:\n1. Commands that will be commonly used, such as how to build, lint, and run tests. Include the necessary commands to develop in this codebase, such as how to run a single test.\n2. High-level code architecture and structure so that future instances can be productive more quickly. Focus on the \"big picture\" architecture that requires reading multiple files to understand.\n\nUsage notes:\n- If there's already a CLAUDE.md, suggest improvements to it.\n- When you make the initial CLAUDE.md, do not repeat yourself and do not include obvious instructions like \"Provide helpful error messages to users\", \"Write unit tests for all new utilities\", \"Never include sensitive information (API keys, tokens) in code or commits\".\n- Avoid listing every component or file structure that can be easily discovered.\n- Don't include generic development practices.\n- If there are Cursor rules (in .cursor/rules/ or .cursorrules) or Copilot rules (in .github/copilot-instructions.md), make sure to include the important parts.\n- If there is a README.md, make sure to include the important parts.\n- Do not make up information such as \"Common Development Tasks\", \"Tips for Development\", \"Support and Documentation\" unless this is expressly included in other files that you read.\n- Be sure to prefix the file with the following text:\n\n```\n# CLAUDE.md\n\nThis file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.\n```",
  "updated_message": "Analyze this codebase and create a CLAUDE.md file for future Claude Code instances.\n\nInclude:\n1. Common commands: build, lint, test, and single test execution\n2. High-level architecture requiring multi-file understanding\n\nRules:\n- Suggest improvements if CLAUDE.md exists\n- Skip obvious practices (error messages, tests, no secrets)\n- Skip file listings, component inventories, generic dev advice\n- Skip fabricated sections unless in existing files\n- Include important parts from: .cursor/rules/, .cursorrules, .github/copilot-instructions.md, README.md\n- Prefix with:\n\n# CLAUDE.md\n\nThis file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.\n"
}
```

## Logging

All requests and responses are logged to `~/.claude-code-fireworks/logs/` with timestamps. Logs are automatically truncated for readability (configurable in code).

## Token Usage

The proxy tracks and converts token usage:
- Fireworks `prompt_tokens` becomes Anthropic `input_tokens`
- Fireworks `completion_tokens` becomes Anthropic `output_tokens`

Token usage is included in both streaming and non-streaming responses.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

MIT
