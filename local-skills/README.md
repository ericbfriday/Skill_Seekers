# Local Skills Library

This directory contains all LLM/Claude skills created locally for development and testing.

## Available Skills

### CircuitPython Skills
- **circuitpython/** - Comprehensive CircuitPython skill (169 pages, 169KB)
  - Documentation: `circuitpython/` folder with SKILL.md and references/
  - Platforms: Claude AI (`.zip`), Google Gemini (`.tar.gz`), OpenAI ChatGPT (`.zip`), Generic Markdown (`.zip`)
  - Source: https://docs.circuitpython.org/en/latest/
  - Coverage: Core modules, libraries, hardware I/O, networking, storage, audio, sensors, displays, power management
  - Files: `local-skills/circuitpython/README.md`

### Ink Skills
- **ink-core/** - Core Ink system modules
  - Documentation: `ink/` folder
  - Platforms: Claude AI (`.zip`)
  - Description: Ink components, Ink system, and Ink compiler

- **ink-ui/** - Ink user interface components
  - Documentation: `ink-ui/` folder
  - Platforms: Claude AI (`.zip`)
  - Description: Ink UI primitives (buttons, cards, inputs, layouts)

- **ink-docs/** - Ink system documentation
  - Documentation: `ink-docs/` folder
  - Platforms: Claude AI (`.zip`)
  - Description: System documentation and guides

- **webtui/** - Web-based terminal UI for Ink
  - Documentation: `webtui/` folder
  - Platforms: Claude AI (`.zip`)
  - Description: Web-based user interface

- **ink-ui-test/** - Ink UI test implementation
  - Documentation: `ink-ui-test/` folder
  - Platforms: Claude AI (`.zip`)
  - Description: Test components and examples

### WebTUI Skills
- **webtui/** - Web-based terminal user interface
  - Documentation: `webtui/` folder
  - Platforms: Claude AI (`.zip`)
  - Description: WebTUI system components

---

## Quick Start

### Installing a Skill

#### Option 1: Claude AI (Recommended)
```bash
# Copy to Claude Code skills directory
cp local-skills/circuitpython/ink/ink-*.zip ~/.claude/skills/

# Restart Claude Code to load the skill
```

#### Option 2: Google Gemini
```bash
# Upload skill to Gemini
# Navigate to https://aistudio.google.com/app/apikey
# Create API key: https://makersuite.google.com/app/apikey
# Select: API & Services > Generative Language API
# Enable: Generative Language API
# Use skill with Gemini AI (GEMINI_API_KEY environment variable)
```

#### Option 3: OpenAI ChatGPT
```bash
# Upload skill to ChatGPT
# Use with existing GPTs or create new one
# Upload .zip file as a knowledge source
```

#### Option 4: Generic Markdown
```bash
# Extract and use markdown files directly
unzip local-skills/circuitpython/circuitpython-markdown.zip -d your_skills/
# Use markdown files in any LLM platform
```

---

## Skill Structure

Each skill folder contains:
```
skill-name/
├── SKILL.md              # Main skill file with overview and examples
├── references/            # Categorized documentation
│   ├── index.md         # Master index
│   ├── category1.md
│   ├── category2.md
│   └── ...
├── assets/               # Custom assets (optional)
└── scripts/              # Custom scripts (optional)
```

---

## Creating New Skills

### Option 1: Use Skill-Seekers CLI
```bash
# Create configuration and scrape
skill-seekers scrape --config configs/your-config.json
skill-seekers package output/your-skill/
```

### Option 2: Manual Creation
```bash
# Create skill directory
mkdir local-skills/your-skill
mkdir local-skills/your-skill/references
mkdir local-skills/your-skill/assets

# Create SKILL.md
cat > local-skills/your-skill/SKILL.md << 'EOF'
---
name: your-skill
description: Description of what this skill does
---
# Your skill content here
EOF

# Create references
echo "# References" > local-skills/your-skill/references/index.md

# Package
skill-seekers package local-skills/your-skill/
```

---

## Maintenance

### Updating Existing Skills

1. Use `skill-seekers enhance` to improve SKILL.md quality
2. Re-scrape documentation when source changes
3. Add new categories by editing reference files
4. Add custom assets or scripts

### Version Management

Track skill versions in README files using semantic versioning:
```
## Version History

### v1.0.0 (January 12, 2026)
- Initial release
- CircuitPython skill with 169 pages
- Ink skills integration
```

---

## License

All skills created locally are for personal/educational use.
Refer to individual skill documentation for specific licensing terms.

---

**Last Updated:** January 12, 2026
