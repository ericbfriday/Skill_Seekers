# CLI MODULE

Main scraping and skill generation logic. Git-style unified CLI with subcommands.

## STRUCTURE

```
cli/
├── main.py               # Git-style dispatcher (skill-seekers <cmd>)
├── adaptors/             # Platform Strategy pattern
├── doc_scraper.py        # Web scraping (1840 LOC) - BFS traversal
├── github_scraper.py     # GitHub repo analysis (1197 LOC)
├── pdf_scraper.py        # PDF extraction
├── unified_scraper.py    # Multi-source combination
├── pattern_recognizer.py # AST pattern detection (1871 LOC)
├── code_analyzer.py      # Deep code analysis (1504 LOC)
├── package_skill.py      # ZIP/tar.gz packaging
└── upload_skill.py       # Platform upload
```

## WHERE TO LOOK

| Task | Location |
|------|----------|
| Add subcommand | `main.py` - create_parser(), route in main() |
| Web scraping logic | `doc_scraper.py` - DocToSkillConverter class |
| GitHub integration | `github_scraper.py` - uses PyGithub |
| PDF extraction | `pdf_scraper.py` - uses PyMuPDF |
| Language detection | `language_detector.py` - 19+ languages |
| Conflict detection | `conflict_detector.py` - docs vs code |
| Platform packaging | `adaptors/{platform}.py` |

## CONVENTIONS

- Each CLI tool has standalone `main()` for direct invocation
- Entry points in `pyproject.toml [project.scripts]`
- Config files: JSON with schema validation (`config_validator.py`)
- Output to `output/{name}/` with `SKILL.md` + `references/`

## LARGE FILE HOTSPOTS

| File | LOC | Responsibility | Refactor Candidate |
|------|-----|----------------|-------------------|
| pattern_recognizer.py | 1871 | AST patterns, 19 languages | Yes - split by language |
| doc_scraper.py | 1840 | BFS, extraction, categorization | Yes - extract phases |
| code_analyzer.py | 1504 | Deep AST analysis | Moderate |
| github_scraper.py | 1197 | GitHub API + analysis | Moderate |
