# Ink Skills for Claude AI

This directory contains Ink-related LLM/Claude skills created locally.

## Available Skills

### Ink Core
- **File:** `ink-core.zip`
- **Description:** Core Ink system modules and functionality
- **Documentation:** See `ink-core/` folder

### Ink UI
- **File:** `ink-ui.zip`
- **Description:** Ink user interface components and layouts
- **Documentation:** See `ink-ui/` folder

### Ink Docs
- **File:** `ink-docs.zip`
- **Description:** Ink system documentation and guides
- **Documentation:** See `ink-docs/` folder

### Ink UI Test
- **File:** `ink-ui-test.zip`
- **Description:** Test implementation of Ink UI components
- **Documentation:** See `ink-ui-test/` folder

### WebTUI
- **File:** `webtui.zip`
- **Description:** Web-based terminal user interface for Ink
- **Documentation:** See `webtui/` folder

---

## Quick Start

### Installing an Ink Skill

```bash
# Copy to local-skills library
cp local-skills/ink/ink-core.zip ~/.claude/skills/

# Restart Claude Code to load the skill
```

### Ink Core Modules

Ink provides a composable architecture for building AI interactions:

- **Ink Components:** Reusable UI primitives (buttons, cards, inputs, layouts)
- **Ink System:** Runtime for component rendering and state management
- **Ink Compiler:** Transforms Ink markup to Claude-ready skills
- **Type System:** Optional TypeScript-like type checking for Ink

### Usage Examples

**Simple UI:**
```
ink_button("Click Me", on_click=lambda: print("Clicked!"))
```

**Complex UI:**
```
ink_panel([
    ink_card(title="Welcome", content="This is Ink"),
    ink_input("Enter your name"),
    ink_button("Submit")
])
```

**Compiler:**
```bash
# Compile Ink markup to skill
ink_compile my_ink_app.ink --output my_skill
```

---

## Documentation Structure

Each Ink skill folder contains:
```
ink-skill-name/
├── SKILL.md              # Main skill file
├── references/            # Categorized docs
│   ├── index.md
│   ├── components.md
│   ├── examples.md
│   └── ...
├── assets/               # Custom assets
└── scripts/              # Custom scripts
```

---

## Related Resources

- **Ink Documentation:** [Official docs link when available]
- **Examples Repository:** [Link to example skills]
- **CircuitPython Skills:** See `../circuitpython/` for CircuitPython skills

---

**Created:** January 12, 2026
