# Webtui - Contributing

**Pages:** 2

---

## Style Guide

**URL:** https://webtui.ironclad.sh/contributing/style-guide

**Contents:**
- Style Guide
- CSS Units
- Selectors
- Documentation

The style guide for code contributed to WebTUI

All selectors in components, utilities, and plugins should be nested within a CSS @layer block

If your Pull Request modifies or adds a new component or utility, please ensure that you add or update the corresponding documentation page

**Examples:**

Example 1 (json):
```json
@layer components {
    input[type='file'],
    [is-=~'fileinput'] {
        /* ... */
    }
}

@layer utils {
    [is-~='tooltip'] {
        /* ... */
    }
}
```

Example 2 (json):
```json
@layer components {
    input[type='file'],
    [is-=~'fileinput'] {
        /* ... */
    }
}

@layer utils {
    [is-~='tooltip'] {
        /* ... */
    }
}
```

---

## Contributing

**URL:** https://webtui.ironclad.sh/contributing/contributing

**Contents:**
- Contributing
- Local Development
- Issues
- Pull Requests

If you are interested in contributing, please ensure that you have read the Style Guide

If you need help on something, join the Discord Server

Ensure Bun is installed

Bug reports, feature requests, and improvements are all welcome as issues.

If you need help, join the Discord Server

Ensure that there is a relevant issue before you open a Pull Request. If there isn’t, open one

Link the relevant issue in your Pull Request with Fixes #issue-number or Closes #issue-number

Ensure that the code in your Pull Request abides by the Style Guide

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/webtui/webtui
```

Example 2 (unknown):
```unknown
git clone https://github.com/webtui/webtui
```

Example 3 (unknown):
```unknown
bun run dev
```

Example 4 (unknown):
```unknown
bun run dev
```

---
