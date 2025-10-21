# Claude Code Fireworks Proxy

A lightweight proxy server that converts Anthropic API requests to Fireworks.ai and vice versa format, enabling Fireworks.ai use with Claude Code without the 5K token streaming limitation.

## Features

- Handle automatically streaming and non-streaming modes based on Claude-Code request.
- Tool/function calling support
- Pre-defined Claude Code user/system prompt replacement feature

## Prerequisites

- Node.js 18 or higher
- A Fireworks.ai API key
- npm or yarn

## Installation

1. Clone the repository:
```bash
git clone https://github.com/TonyGeez/claude-code-fireworks.git
cd claude-code-fireworks
```

2. Install dependencies:
```bash
npm install
```

3. Create a `.env` file in the root directory:
```env
LISTEN_HOST=127.0.0.1
LISTEN_PORT=3000

FIREWORKS_BASE=https://api.fireworks.ai/inference/v1
FIREWORKS_API_KEY=your_api_key_here
FIREWORKS_MODEL=accounts/fireworks/models/glm-4p5
```

## Usage

### Build

```bash
npm run build
```
### Start proxy 
```bash
npm run start
```

### Start Claude Code (In another session or use pm2) 
```bash
export ANTHROPIC_BASE_URL=http://127.0.0.1:3000
claude
```

## Configuration

### Environment Variables

- `FIREWORKS_API_KEY` - Your Fireworks.ai API key (required)
- `FIREWORKS_MODEL` - The Fireworks model to use (required)
- `LISTEN_HOST` - Host to bind the server to (default: 127.0.0.1)
- `LISTEN_PORT` - Port to listen on (default: 3000)

### Message Replacements

You can replace the pre-defined Claude prompt by adding JSON files to the `replace/` directory:

```json
{
  "original_message": "Text to find",
  "updated_message": "Text to replace with"
}
```

The proxy will automatically load all `.json` files from the `replace/` directory on startup and apply exact string matches to message content. 

You must log output and input from logs to use the exact message. Here an example of replacement of the prompt triggered when execute the /init command in Claude Code:

```json
{
  "original_message": "Text to find",
  "updated_message": "Text to replace with"
}
```


## Logging

All requests and responses are logged to the `logs/` directory with timestamps. Logs are automatically truncated for readability (configurable in code).

## Token Usage

The proxy tracks and converts token usage:
- Fireworks `prompt_tokens` becomes Anthropic `input_tokens`
- Fireworks `completion_tokens` becomes Anthropic `output_tokens`

Token usage is included in both streaming and non-streaming responses.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

