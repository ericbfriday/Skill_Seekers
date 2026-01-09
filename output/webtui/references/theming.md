# Webtui - Theming

**Pages:** 3

---

## Gruvbox Theme

**URL:** https://webtui.ironclad.sh/plugins/theme-gruvbox

**Contents:**
- Gruvbox Theme
- Installation
- Variants
- Components
  - Typography
  - Badge
  - Button
- CSS Variables

A port of the Gruvbox color palette to WebTUI.

Install the theme with your preferred package manager

Ensure you import the theme after all the other stylesheets from @webtui/css or the styles will not be applied.

Set the data-webtui-theme attribute on the <html> tag or a container element.

To only apply the theme to a specific element:

Supports dark and light modes, each with hard, medium, and soft contrast levels.

Components affected/modified by the theme:

Adds additional variants to badges matching Gruvbox accent colors.

Adds additional variants to buttons matching Gruvbox accent colors.

Adds the following CSS variables within the base layer, each of which change based on the theme variant

The base background/foreground colors use the following CSS variables from the Gruvbox palette

**Examples:**

Example 1 (python):
```python
bun i @webtui/theme-gruvbox
npm i @webtui/theme-gruvbox
yarn add @webtui/theme-gruvbox
pnpm i @webtui/theme-gruvbox
```

Example 2 (python):
```python
bun i @webtui/theme-gruvbox
npm i @webtui/theme-gruvbox
yarn add @webtui/theme-gruvbox
pnpm i @webtui/theme-gruvbox
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-gruvbox';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-gruvbox';
```

---

## Theming

**URL:** https://webtui.ironclad.sh/start/theming

**Contents:**
- Theming
- CSS Variables
  - Font Styles
  - Colors
  - Light & Dark
- Theme Plugins
  - Using Multiple Theme Accents

Theming can be done by editing the CSS variables in the base stylesheet, or if you install a theme plugin

@webtui/css ships with a set of CSS variables that control font styles and background/foreground colors

These can be customized with the CSS :root selector on the base layer

The base theme consists of four background levels and three foreground levels

Additional color accents/variants can be added to components individually with CSS or by importing a theme plugin

Shown below is a screenshot showing the background and foreground colors of a basic light theme, dark theme, Nord, and Catppuccin

@webtui/css ships with a basic light and dark theme

To enable the dark theme, simply add data-webtui-theme="dark" to the <html> tag, or any element that should inherit the theme colors

Theme plugins change the base colors and often include additional color variants and styles for individual components

Check out the Plugins page for a list of available themes

Some themes provide different accent colors such as --red, --green, etc

Not all themes use the same accent names (e.g. --blue vs --aqua)

If you intend to use multiple themes, you can use CSS variable fallbacks to use the desired accent color

**Examples:**

Example 1 (css):
```css
@layer base {
    :root {
        --font-size: 18px;
        --font-family: 'JetBrainsMono', monospace;
    }
}
```

Example 2 (css):
```css
@layer base {
    :root {
        --font-size: 18px;
        --font-family: 'JetBrainsMono', monospace;
    }
}
```

Example 3 (css):
```css
@layer base {
    :root {
        --font-size: 16px;
        --line-height: 1.3;
        /* Font weight for bold text */
        --font-weight-bold: 700;
        /* Font weight for regular text */
        --font-weight-normal: 400;
        --font-family: monospace;
    }
}
```

Example 4 (css):
```css
@layer base {
    :root {
        --font-size: 16px;
        --line-height: 1.3;
        /* Font weight for bold text */
        --font-weight-bold: 700;
        /* Font weight for regular text */
        --font-weight-normal: 400;
        --font-family: monospace;
    }
}
```

---

## Everforest Theme

**URL:** https://webtui.ironclad.sh/plugins/theme-everforest

**Contents:**
- Everforest Theme
- Installation
- Variants
- Components
  - Typography
  - Badge
  - Button
- CSS Variables

A port of the Everforest color palette to WebTUI.

Install the theme with your preferred package manager

Ensure you import the theme after all the other stylesheets from @webtui/css or the styles will not be applied.

Set the data-webtui-theme attribute on the <html> tag or a container element.

To only apply the theme to a specific element:

Supports dark and light modes, each with hard, medium, and soft contrast levels.

Components affected/modified by the theme:

Adds additional variants to badges matching everforest accent colors.

Adds additional variants to buttons matching everforest accent colors.

Adds the following CSS variables within the base layer, each of which change based on the theme variant

The base background/foreground colors use the following CSS variables from the Everforest palette

**Examples:**

Example 1 (python):
```python
bun i @webtui/theme-everforest
npm i @webtui/theme-everforest
yarn add @webtui/theme-everforest
pnpm i @webtui/theme-everforest
```

Example 2 (python):
```python
bun i @webtui/theme-everforest
npm i @webtui/theme-everforest
yarn add @webtui/theme-everforest
pnpm i @webtui/theme-everforest
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-everforest';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-everforest';
```

---
