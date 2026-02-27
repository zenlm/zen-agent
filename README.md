# Zen Agent — Agentic AI Framework

A Python framework for building AI agents with tool use, planning, memory, and multi-step reasoning capabilities. Powered by Zen models from [Zen LM](https://zenlm.org).

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)

## Features

- **Tool Calling**: Define custom tools; agents invoke them autonomously
- **Code Execution**: Built-in sandboxed Python interpreter for code tasks
- **Web Browsing**: Agent-driven web search and page retrieval
- **Multi-step Planning**: ReAct and function-calling agent loops
- **RAG Support**: Retrieval-augmented generation over long documents
- **MCP Integration**: Native Model Context Protocol support
- **GUI**: Gradio-based web interface for rapid prototyping

## Installation

```bash
# Stable release
pip install zen-agent

# With optional extras
pip install "zen-agent[gui,rag,code_interpreter,mcp]"
```

## Quick Start

```python
from zen_agent.agents import Assistant

# Configure the model
llm_cfg = {
    "model": "zenlm/zen-coder",
    "model_server": "http://localhost:8000/v1",
    "api_key": "EMPTY",
    "generate_cfg": {"top_p": 0.8},
}

# Create an agent with tools and files
bot = Assistant(
    llm=llm_cfg,
    system_message="You are a helpful coding assistant.",
    function_list=["code_interpreter"],
    files=["./docs/reference.pdf"],
)

# Run the agent
messages = []
while True:
    query = input("user: ")
    messages.append({"role": "user", "content": query})
    for response in bot.run(messages=messages):
        pass
    messages.extend(response)
```

### Launch Web UI

```python
from zen_agent.gui import WebUI
WebUI(bot).run()
```

### Custom Tool

```python
from zen_agent.tools.base import BaseTool, register_tool

@register_tool("search_docs")
class SearchDocs(BaseTool):
    description = "Search internal documentation for a query."
    parameters = [{"name": "query", "type": "string", "required": True}]

    def call(self, params: str, **kwargs) -> str:
        import json5
        query = json5.loads(params)["query"]
        # ... your search logic ...
        return json5.dumps({"results": []})
```

## Models

Zen Agent works with any OpenAI-compatible endpoint. Recommended models:

| Model | Use Case | Size |
|-------|----------|------|
| `zenlm/zen-coder` | Code generation and tool use | 4B |
| `zenlm/zen-max` | Complex multi-step reasoning | 671B MoE |

Deploy locally with vLLM:

```bash
vllm serve zenlm/zen-coder --port 8000
```

## MCP Support

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/files"]
    }
  }
}
```

See [Hanzo MCP](https://github.com/hanzoai/mcp) for 260+ pre-built MCP tools.

## Links

- **Website**: https://zenlm.org
- **Models**: https://huggingface.co/zenlm
- **GitHub**: https://github.com/zenlm
- **Hanzo AI**: https://hanzo.ai

## License

Apache 2.0

---

**Zen AI**: Clarity Through Intelligence
