# Zen Agent

Agentic framework for Zen models with MCP support, tool use, and multi-step planning.

Zen Agent is a framework for building LLM applications on top of Zen models. It provides instruction following, tool usage, planning, memory, and Model Context Protocol (MCP) integration out of the box.

## Features

- **Tool use**: Function calling, code execution, web search, file operations
- **MCP support**: Connect any MCP server as a tool source
- **RAG**: Retrieval-augmented generation with document ingestion
- **Planning**: Multi-step task decomposition and execution
- **Memory**: Persistent and session-scoped memory backends
- **GUI**: Optional Gradio-based chat interface

## Installation

```bash
# Stable release
pip install -U "zen-agent[gui,rag,code_interpreter,mcp]"

# Minimal install
pip install -U zen-agent

# Development
git clone https://github.com/zenlm/zen-agent.git
cd zen-agent
pip install -e ".[gui,rag,code_interpreter,mcp]"
```

## Quick Start

### Connect a model

Zen Agent works with any OpenAI-compatible endpoint. Point it at a local Zen model served via vLLM or Ollama, or any hosted API:

```python
import os
os.environ["ZEN_API_KEY"] = "your-api-key"
os.environ["ZEN_API_BASE"] = "http://localhost:8000/v1"  # or any OpenAI-compatible URL
```

### Basic agent

```python
from zen_agent import Assistant

assistant = Assistant(
    llm_config={"model": "zenlm/zen-coder-flash", "temperature": 0.7},
    tools=["web_search", "code_interpreter"],
    system_message="You are a helpful Zen AI assistant.",
)
response = assistant.run("Search for the latest AI research papers and summarize them.")
print(response.messages[-1]["content"])
```

### MCP tool integration

```python
from zen_agent import Assistant
from zen_agent.mcp import MCPClient

mcp = MCPClient("stdio", command="npx", args=["-y", "@modelcontextprotocol/server-filesystem", "/tmp"])

assistant = Assistant(
    llm_config={"model": "zenlm/zen-max"},
    tools=[mcp],
)
response = assistant.run("List all Python files in /tmp and summarize their contents.")
```

### Multi-agent pipeline

```python
from zen_agent import Agent, UserProxyAgent

planner = Agent(
    name="Planner",
    llm_config={"model": "zenlm/zen-max"},
    system_message="Break down complex tasks into actionable steps.",
)

executor = Agent(
    name="Executor",
    llm_config={"model": "zenlm/zen-coder-flash"},
    system_message="Execute tasks using available tools.",
    tools=["code_interpreter", "bash"],
)

user = UserProxyAgent(name="User", human_input_mode="NEVER")
user.initiate_chat(planner, message="Build and test a REST API in Python.")
```

## GUI

```bash
# Launch web interface
python -m zen_agent.gui
```

## Architecture

```
zen_agent/
├── llm/          # LLM backends (OpenAI-compatible, local)
├── tools/        # Built-in tools (search, code, files, MCP)
├── memory/       # Memory backends (in-memory, vector, persistent)
├── planning/     # Task decomposition and step tracking
├── gui/          # Gradio chat interface
└── examples/     # Example scripts and notebooks
```

## Examples

See the `examples/` directory for:
- `basic_assistant.py` - Simple question answering
- `code_agent.py` - Agentic coding with tool use
- `mcp_tools.py` - Connecting MCP servers
- `multi_agent.py` - Multi-agent coordination
- `rag_pipeline.py` - Document retrieval and QA

## Links

- Models: [huggingface.co/zenlm](https://huggingface.co/zenlm)
- Docs: [zenlm.org](https://zenlm.org)
- Issues: [github.com/zenlm/zen-agent/issues](https://github.com/zenlm/zen-agent/issues)

## License

Apache 2.0 — Copyright 2024 Zen LM Authors