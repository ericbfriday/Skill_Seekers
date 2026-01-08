# MCP TOOLS

Domain-organized tool implementations for FastMCP server.

## STRUCTURE

| File | Tools | Domain |
|------|-------|--------|
| config_tools.py | list_configs, generate_config, validate_config | Config management |
| scraping_tools.py | scrape_docs, scrape_github, scrape_pdf, estimate_pages, detect_patterns | Content extraction |
| packaging_tools.py | package_skill, upload_skill, install_skill | Output generation |
| splitting_tools.py | split_config, generate_router | Large doc handling |
| source_tools.py | fetch_config, submit_config, add/list/remove_config_source | Private repos |

## ADDING TOOL

```python
# In appropriate {domain}_tools.py
from skill_seekers.mcp.server_fastmcp import mcp

@mcp.tool()
async def my_tool(arg1: str, arg2: int = 10) -> str:
    """Tool description shown to AI agents."""
    # Implementation
    return "result"
```

## CALLING CLI

Tools typically wrap CLI scripts via subprocess:

```python
result = await run_subprocess_with_streaming([
    sys.executable,
    "-m", "skill_seekers.cli.doc_scraper",
    "--config", config_path
])
```

## CONVENTIONS

- All tools async (`async def`)
- Return strings (MCP text content)
- Use `run_subprocess_with_streaming` for CLI calls
- Type hints required for AI agent introspection
