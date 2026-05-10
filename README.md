# 🥬 ColeSlaw

**A fast, hackable, Python-native AI coding assistant that works with any model.**

ColeSlaw is a lightweight terminal-based AI assistant built in readable Python. It gives you the power of tools like Claude Code — file editing, shell access, multi-agent workflows, and more — but works with **any LLM provider** and runs entirely from your terminal with zero build steps.

---

## What It Does

ColeSlaw drops you into an interactive REPL where you can chat with an AI that has full access to your codebase. It can:

- **Read, write, and edit files** with git-style diffs
- **Run shell commands** and react to the output
- **Search your codebase** with glob and grep
- **Browse the web** and fetch documentation
- **Manage tasks** with dependency tracking
- **Spawn sub-agents** for parallel work (code review, research, testing)
- **Remember context** across sessions with persistent memory
- **Plan before coding** with a structured read-only plan mode

All of this happens through a streaming agent loop that's only ~174 lines of Python (`agent.py`).

---

## Why ColeSlaw Over Alternatives

### vs. Claude Code
| | Claude Code | ColeSlaw |
|---|---|---|
| **Models** | Anthropic only | 8+ providers — Claude, GPT, Gemini, DeepSeek, Qwen, Ollama, and more |
| **Local models** | ❌ | ✅ Ollama, LM Studio, vLLM — fully offline |
| **Source code** | 283K lines of compiled TypeScript | ~12K lines of readable Python |
| **Build step** | Yes (Bun + esbuild) | No — `pip install` and run |
| **Extensibility** | Closed | `register_tool()` at runtime, MCP servers, git plugins, Markdown skills |

### vs. Other Open-Source Assistants
- **Zero setup** — `pip install -r requirements.txt && python coleslaw.py`. That's it.
- **Truly multi-provider** — switch models with `--model` or `/model` at runtime. No config files, no rebuilds.
- **Hackable in minutes** — the entire agent loop fits on one screen. Fork it, extend it, make it yours.
- **Batteries included** — voice input, Telegram/Slack/WeChat bridges, brainstorm mode, autonomous agents, checkpoint/rewind, cloud sync — all built in.

---

## Quick Start

### Install

```bash
git clone https://github.com/SamirSengupta/ColeSlaw.git
cd ColeSlaw
pip install -r requirements.txt
```

### Run

```bash
# With a local model (Ollama)
python coleslaw.py --model ollama/qwen2.5-coder

# With a cloud model
export ANTHROPIC_API_KEY=sk-ant-...
python coleslaw.py --model claude-sonnet-4-6

export OPENAI_API_KEY=sk-...
python coleslaw.py --model gpt-4o

export GEMINI_API_KEY=AIza...
python coleslaw.py --model gemini/gemini-2.0-flash
```

### Or Install Globally with `uv`

```bash
uv tool install .
coleslaw --model ollama/qwen2.5-coder
```

---

## Supported Models

### Cloud APIs

| Provider | Example Models | Env Variable |
|---|---|---|
| **Anthropic** | `claude-opus-4-6`, `claude-sonnet-4-6` | `ANTHROPIC_API_KEY` |
| **OpenAI** | `gpt-4o`, `gpt-4.1`, `o3-mini` | `OPENAI_API_KEY` |
| **Google** | `gemini/gemini-2.5-pro-preview-03-25`, `gemini/gemini-2.0-flash` | `GEMINI_API_KEY` |
| **DeepSeek** | `deepseek/deepseek-chat`, `deepseek/deepseek-reasoner` | `DEEPSEEK_API_KEY` |
| **Qwen** | `qwen/qwen-max`, `qwen/qwen-turbo` | `DASHSCOPE_API_KEY` |
| **Kimi** | `kimi/moonshot-v1-128k` | `MOONSHOT_API_KEY` |
| **Zhipu** | `zhipu/glm-4-plus` | `ZHIPU_API_KEY` |
| **MiniMax** | `minimax/MiniMax-Text-01` | `MINIMAX_API_KEY` |

### Local Models (via Ollama)

```bash
ollama pull qwen2.5-coder    # Best for coding
ollama pull llama3.3          # General purpose
ollama pull deepseek-r1       # Reasoning
ollama pull gemma4            # Google's latest

python coleslaw.py --model ollama/qwen2.5-coder
```

Also works with **LM Studio**, **vLLM**, or any OpenAI-compatible endpoint via the `custom/` prefix.

---

## Key Commands

Once inside the REPL, use slash commands:

| Command | What it does |
|---|---|
| `/model <name>` | Switch models on the fly |
| `/help` | Show all available commands |
| `/save` / `/load` | Save and restore sessions |
| `/memory` | View and search persistent memories |
| `/voice` | Voice input (local Whisper, no API key needed) |
| `/image` | Send a clipboard image to the model |
| `/brainstorm` | Multi-persona AI debate on any topic |
| `/worker` | Auto-implement pending tasks |
| `/agent` | Launch autonomous background agents |
| `/ssj` | Developer power menu with workflow shortcuts |
| `/plan` | Enter structured planning mode before coding |
| `/checkpoint` | List/restore conversation + file snapshots |
| `/compact` | Compress conversation history to save tokens |
| `/telegram` | Control ColeSlaw from your phone via Telegram |
| `/slack` | Connect to a Slack channel |
| `/cloudsave` | Sync sessions to GitHub Gists |
| `/status` | Show current session info |
| `/doctor` | Diagnose installation health |

Type `!command` to run shell commands directly (e.g., `!git status`).

---

## Built-in Tools

The AI has access to 27+ tools:

- **File ops** — `Read`, `Write`, `Edit`, `Glob`, `Grep`
- **Shell** — `Bash` (with configurable timeout)
- **Web** — `WebFetch`, `WebSearch`
- **Notebooks** — `NotebookEdit` (direct `.ipynb` manipulation)
- **Diagnostics** — `GetDiagnostics` (pyright → mypy → flake8 chain)
- **Memory** — `MemorySave`, `MemorySearch`, `MemoryList`, `MemoryDelete`
- **Multi-agent** — `Agent`, `SendMessage`, `CheckAgentResult`, `ListAgentTasks`
- **Tasks** — `TaskCreate`, `TaskUpdate`, `TaskGet`, `TaskList`
- **Planning** — `EnterPlanMode`, `ExitPlanMode`
- **Skills** — `Skill`, `SkillList`
- **Timers** — `SleepTimer` (for monitoring and reminders)
- **Interactive** — `AskUserQuestion` (pause for user input mid-task)

Plus any tools from **MCP servers** and **plugins** are auto-registered at startup.

---

## Extending ColeSlaw

### Custom Tools
```python
from tool_registry import ToolDef, register_tool

register_tool(ToolDef(
    name="MyTool",
    schema={"name": "MyTool", "description": "...", "input_schema": {...}},
    func=my_function,
    read_only=True,
))
```

### MCP Servers
Connect any MCP server (stdio, SSE, or HTTP). Tools are auto-discovered and available to the AI immediately.

### Plugins
Install plugins from git repos or local paths:
```
/plugin install https://github.com/user/my-plugin.git
```

### Skills
Drop a Markdown file into `~/.coleslaw/skills/` to create reusable prompt templates.

---

## Configuration

Config is stored at `~/.coleslaw/config.json`. Key settings:

| Setting | Default | Description |
|---|---|---|
| `model` | `ollama/gemma4:e4b` | Default model |
| `permission_mode` | `auto` | `auto` / `accept-all` / `manual` |
| `max_tokens` | `40000` | Max output tokens |
| `verbose` | `false` | Show token counts and tool details |
| `thinking` | `false` | Enable extended thinking |

Set values via the REPL (`/config key=value`) or `config.json` directly.

API keys can be set as environment variables or in the config file.

---

## CLI Options

```
python coleslaw.py [OPTIONS] [PROMPT]

Options:
  -m, --model MODEL     Model to use (e.g., gpt-4o, ollama/llama3.3)
  -p, --print           Non-interactive mode: run prompt and exit
  --accept-all          Auto-approve all tool operations
  --verbose             Show detailed output
  --thinking            Enable extended thinking
```

---

## Project Structure

```
coleslaw.py         Main REPL and entry point
agent.py            Core agent loop (~174 lines)
providers.py        Multi-provider LLM abstraction
tools.py            Built-in tool implementations
config.py           Configuration management
context.py          System prompt construction
memory/             Persistent memory system
task/               Task management with dependencies
skill/              Reusable prompt templates
plugin/             Plugin system
mcp/                MCP server integration
bridges/            Telegram, Slack, WeChat bridges
commands/           Slash command handlers
ui/                 Terminal rendering (ANSI + Rich)
modular/            Optional feature modules (video, voice)
checkpoint/         Session snapshot and rewind
```

---

## License

MIT

---

**Built by [Samir Sengupta](https://github.com/SamirSengupta)**
