# PROJECT KNOWLEDGE BASE

**Generated:** 2026-01-08
**Commit:** 26474c2
**Branch:** development

## OVERVIEW

Python tool converting documentation websites, GitHub repos, and PDFs into LLM skills for 4 platforms (Claude AI, Google Gemini, OpenAI ChatGPT, Generic Markdown). Uses Strategy Pattern with platform adaptors.

## STRUCTURE

```
Skill_Seekers/
├── src/skill_seekers/
│   ├── cli/              # 36 CLI tools, git-style dispatcher (main.py)
│   │   └── adaptors/     # Platform Strategy pattern (claude, gemini, openai, markdown)
│   └── mcp/              # FastMCP server (18 tools, stdio + HTTP transport)
│       └── tools/        # Tool implementations by domain
├── tests/                # 700+ tests, pytest, extensive mocking
├── configs/              # Preset scraping configs (JSON schema)
└── docs/                 # User/dev documentation
```

## WHERE TO LOOK

| Task | Location | Notes |
|------|----------|-------|
| Add CLI command | `src/skill_seekers/cli/main.py` | Git-style subparser, calls existing `main()` |
| Add platform | `cli/adaptors/{platform}.py` | Inherit `SkillAdaptor`, register in `__init__.py` |
| Add MCP tool | `mcp/tools/{domain}_tools.py` | Register with FastMCP decorator |
| Scraper logic | `cli/doc_scraper.py` (1840 LOC) | BFS traversal, content extraction |
| Pattern detection | `cli/pattern_recognizer.py` (1871 LOC) | AST-based, multi-language |
| Code analysis | `cli/code_analyzer.py` (1504 LOC) | Deep AST parsing |
| Run tests | `pip install -e . && pytest tests/ -v` | MUST install package first (src/ layout) |

## CONVENTIONS

- **src/ layout**: `pip install -e .` required before tests (PEP 517/518)
- **Entry points**: `pyproject.toml [project.scripts]` - both unified CLI and individual tools
- **Optional deps**: Platform-specific in `[project.optional-dependencies]` (gemini, openai, all-llms)
- **MCP deprecation**: `server.py` → shim, use `server_fastmcp.py` directly (v3.0.0 removal)

## ANTI-PATTERNS (THIS PROJECT)

- Never suppress type errors (`as any`, `@ts-ignore` N/A - Python only)
- Never commit without tests passing (`pytest tests/ -v` - 700+ tests)
- Never hardcode platform logic - use adaptors
- Never skip `pip install -e .` before tests

## COMMANDS

```bash
# Setup
pip install -e .                    # REQUIRED before tests
pip install -e ".[all-llms]"        # All platform deps

# Test
pytest tests/ -v                    # All tests
pytest tests/test_mcp_fastmcp.py -v # MCP tests

# Run
skill-seekers scrape --config configs/react.json
skill-seekers package output/react/ --target gemini
python -m skill_seekers.mcp.server_fastmcp  # MCP server

# Build
uv build && uv publish              # PyPI release
```

## NOTES

- **Large files**: `doc_scraper.py` (1840), `pattern_recognizer.py` (1871), `code_analyzer.py` (1504) - refactoring candidates
- **Legacy shim**: `mcp/server.py` delegates to `server_fastmcp.py` with deprecation warning
- **Test requirement**: Tests import from installed package, not source - fails without `pip install -e .`
- **Version**: 2.5.2 in `pyproject.toml`, 2.5.1 in `cli/main.py` (sync needed)
