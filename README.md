# PLO

Your AI-powered personal advisory board. PLO brings together domain experts — health, fitness, tech, finance — to provide holistic advice for your life decisions.

## Project Structure

```
.
├── backend/                      # Python agent runtime (LangGraph + LangChain)
│   ├── app/
│   │   └── gateway/              # FastAPI REST API (models, skills, memory, uploads)
│   ├── packages/harness/         # Core agent framework
│   │   ├── deerflow/agents/      # Lead agent, sub-agents, checkpointer
│   │   ├── deerflow/sandbox/     # Docker/local sandbox execution
│   │   ├── deerflow/tools/       # Built-in tools (bash, file, search)
│   │   ├── deerflow/skills/      # Skill loader and installer
│   │   └── deerflow/memory/      # Long-term memory storage
│   ├── tests/                    # Test suite
│   └── docs/                     # Architecture and API docs
├── frontend/                     # Next.js React web interface
│   ├── src/app/                  # App Router pages
│   ├── src/components/           # React components (chat, workspace, settings)
│   └── src/hooks/                # Custom React hooks
├── skills/                       # Agent skills (research, analysis, etc.)
│   └── public/                   # Built-in skills (deep-research, data-analysis, etc.)
└── docker/                       # Container configurations
    ├── docker-compose.yaml       # Production orchestration
    ├── docker-compose-dev.yaml   # Development with hot-reload
    └── nginx/                    # Reverse proxy configs
```

## Quick Start

### Docker (Recommended)

```bash
# Clone and configure
git clone https://github.com/bytedance/deer-flow.git plo
cd plo
make config

# Add your API keys to .env
# Edit config.yaml to set your preferred models

# Start services
make docker-init
make docker-start
```

Access at http://localhost:2026

### Local Development

Requirements: Python 3.12+, Node.js 22+, pnpm, uv

```bash
make check      # Verify prerequisites
make install    # Install dependencies
make dev        # Start development server
```

## Configuration

Edit `config.yaml` to add your models:

```yaml
models:
  - name: gpt-4
    display_name: GPT-4
    use: langchain_openai:ChatOpenAI
    model: gpt-4
    api_key: $OPENAI_API_KEY
    max_tokens: 4096
```

Set API keys in `.env`:

```bash
OPENAI_API_KEY=your-key
TAVILY_API_KEY=your-key
```

### MCP & Skills

MCP (Model Context Protocol) servers and custom skills extend PLO's capabilities. Configure them in `extensions_config.json`:

```bash
cp extensions_config.example.json extensions_config.json
```

**Example MCP servers:**

```json
{
  "mcpServers": {
    "filesystem": {
      "enabled": true,
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/files"]
    },
    "github": {
      "enabled": true,
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "$GITHUB_TOKEN" }
    }
  }
}
```

Supported MCP types: `stdio`, `http`, `sse`. HTTP/SSE servers support OAuth authentication.

## Migration Guide

To move your data to a new machine:

```bash
# 1. Stop services
make down

# 2. Backup data and config
tar -czf plo-backup.tar.gz \
  backend/.deer-flow/ \
  config.yaml \
  .env

# 3. Transfer to new machine and extract
# On new machine:
tar -xzf plo-backup.tar.gz
make docker-start
```

**What's included:**
- `backend/.deer-flow/` — all conversations, uploaded files, outputs, and memory
- `config.yaml` — model configuration and settings
- `.env` — API keys and secrets

**What's not included:**
- Custom skills in `skills/custom/` — back up separately if you have any

## Credits

PLO is built on [DeerFlow](https://github.com/bytedance/deer-flow) by ByteDance. We thank the original authors for the solid foundation.
