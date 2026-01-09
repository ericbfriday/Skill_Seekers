# Webtui - Plugins

**Pages:** 3

---

## Plugins

**URL:** https://webtui.ironclad.sh/start/plugins

**Contents:**
- Plugins
- Official Plugins
  - Themes
- Community Plugins

Plugins are external stylesheets that are designed to work alongside @webtui/css

None yet, be the first!

---

## Developing Plugins

**URL:** https://webtui.ironclad.sh/plugins/plugin-dev

**Contents:**
- Developing Plugins
  - Style Layers

The requirements for developing a plugin are very simple:

To define styles for a specific layer, use the CSS @layer at-rule [MDN Reference]

**Examples:**

Example 1 (python):
```python
@layer base {
    /* ... */
}
@layer utils {
    /* ... */
}
@layer components {
    /* ... */
}
```

Example 2 (python):
```python
@layer base {
    /* ... */
}
@layer utils {
    /* ... */
}
@layer components {
    /* ... */
}
```

---

## Nerd Font Plugin

**URL:** https://webtui.ironclad.sh/plugins/plugin-nf

**Contents:**
- Nerd Font Plugin
- Installation

Provides additional glyphs from popular icon packs such as Font Awesome, Devicons, Octicons, and more (see https://nerdfonts.com)

See https://nerdfonts.com/cheat-sheet for a reference of available icons you can use

Install the plugin with your preferred package manager

Import the plugin at the end of your import chain

Add “Symbols Nerd Font” to the end of the --font-family fallback stack

Now you can use nerd font icons in your HTML and CSS

**Examples:**

Example 1 (python):
```python
bun i @webtui/plugin-nf
npm i @webtui/plugin-nf
yarn add @webtui/plugin-nf
pnpm i @webtui/plugin-nf
```

Example 2 (python):
```python
bun i @webtui/plugin-nf
npm i @webtui/plugin-nf
yarn add @webtui/plugin-nf
pnpm i @webtui/plugin-nf
```

Example 3 (python):
```python
@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/plugin-nf';
```

Example 4 (python):
```python
@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/plugin-nf';
```

---
