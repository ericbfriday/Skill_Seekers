# MCP SERVER

FastMCP server providing 18 tools for AI agent integration. Supports stdio (Claude Code, VS Code) and HTTP (Cursor, Windsurf) transport.

## STRUCTURE

```
mcp/
├── server_fastmcp.py   # PRIMARY - FastMCP implementation
├── server.py           # DEPRECATED shim (v3.0.0 removal)
├── server_legacy.py    # Old implementation reference
└── tools/              # Tool implementations by domain
```

## TRANSPORT MODES

| Mode | Agents | How |
|------|--------|-----|
| stdio | Claude Code, VS Code + Cline | Subprocess, stdin/stdout |
| HTTP | Cursor, Windsurf, IntelliJ | HTTP service on port 8765 |

## RUNNING

```bash
# stdio (default)
python -m skill_seekers.mcp.server_fastmcp

# HTTP
python -m skill_seekers.mcp.server_fastmcp --transport http --port 8765
```

## ADDING TOOLS

1. Create/modify `tools/{domain}_tools.py`
2. Use FastMCP decorator pattern (see existing tools)
3. Tool auto-registered on import
4. Test with `pytest tests/test_mcp_fastmcp.py -v`

## ANTI-PATTERNS

- Never import from `server.py` directly (deprecated shim)
- Never hardcode paths - use `Path(__file__).parent` patterns
- Never block event loop - all tools must be async
