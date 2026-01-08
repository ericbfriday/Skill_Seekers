# TESTS

700+ pytest tests. MUST run `pip install -e .` before tests (src/ layout).

## STRUCTURE

```
tests/
├── conftest.py           # Fixtures, package install check
├── test_*.py             # Main test modules
├── test_adaptors/        # Platform adaptor tests
└── fixtures/             # Test data
```

## RUNNING

```bash
pip install -e .                    # REQUIRED first
pytest tests/ -v                    # All tests
pytest tests/test_mcp_fastmcp.py -v # MCP only
pytest tests/ -k "config"           # Pattern match
pytest tests/ --cov=src/skill_seekers --cov-report=html
```

## CONVENTIONS

- Tests import from installed package (`from skill_seekers.cli import ...`)
- Extensive mocking - avoid network calls in unit tests
- `conftest.py` shows helpful error if package not installed
- Mark slow tests: `@pytest.mark.slow`
- Mark async tests: `@pytest.mark.asyncio`

## KEY TEST FILES

| File | Coverage |
|------|----------|
| test_config_validation.py | Config schema |
| test_github_scraper.py | GitHub integration |
| test_mcp_fastmcp.py | MCP server tools |
| test_adaptors/*.py | Platform adaptors |
