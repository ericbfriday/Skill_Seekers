# Webtui_Docs - Components

**Pages:** 22

---

## Button

**URL:** https://webtui.ironclad.sh/components/button

**Contents:**
- Button
- Import
- Usage
- Examples
  - Variants
  - Box Borders
  - Sizes
  - disabled
- Reference
  - Properties

To add box borders to button, import the Box Utility stylesheet (optional)

Apply is-="button" to any HTML element to style it as a button

Available variants match the base theme colors

Requires the Box Utility

Buttons automatically switch between --button-primary and --button-secondary based on whether box- is applied or not

To extend the Button stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/button.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/button.css';
```

Example 3 (python):
```python
@import '@webtui/css/utils/box.css';
```

Example 4 (python):
```python
@import '@webtui/css/utils/box.css';
```

---

## Switch

**URL:** https://webtui.ironclad.sh/components/switch

**Contents:**
- Switch
- Import
- Usage
- Examples
  - Checked/Unchecked
  - Disabled
  - Size
  - Bar Size
- Reference
  - Properties

Use the checked attribute to set the initial value of the switch checkbox

Use the disabled attribute to disable the switch

Use the size- attribute to set the size of the switch

Set the bar- attribute to thin or line to set the size of the switch bar

To extend the Checkbox stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/switch.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/switch.css';
```

Example 3 (jsx):
```jsx
<label>
    <input type="checkbox" is-="switch" />
    Switch
</label>
```

Example 4 (jsx):
```jsx
<label>
    <input type="checkbox" is-="switch" />
    Switch
</label>
```

---

## Accordion

**URL:** https://webtui.ironclad.sh/components/accordion

**Contents:**
- Accordion
- Import
- Usage
- Examples
  - Nesting
- Reference
  - Extending
  - Scope

Displays an accordion

To extend the Badge stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/accordion.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/accordion.css';
```

Example 3 (jsx):
```jsx
<details is-="accordion">
    <summary>Summary title</summary>
    <p>Accordion content</p>
</details>
```

Example 4 (jsx):
```jsx
<details is-="accordion">
    <summary>Summary title</summary>
    <p>Accordion content</p>
</details>
```

---

## Checkbox

**URL:** https://webtui.ironclad.sh/components/checkbox

**Contents:**
- Checkbox
- Import
- Usage
- Examples
  - Checked/Unchecked
  - Disabled
- Reference
  - Extending
  - Scope

To extend the Checkbox stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/checkbox.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/checkbox.css';
```

Example 3 (jsx):
```jsx
<label>
    <input type="checkbox" />
    Checkbox
</label>
```

Example 4 (jsx):
```jsx
<label>
    <input type="checkbox" />
    Checkbox
</label>
```

---

## Typography

**URL:** https://webtui.ironclad.sh/components/typography

**Contents:**
- Typography
- Import
- Usage
  - <h1>-<h6>
  - <p>
  - Inline Elements
  - <blockquote>
  - <ol>
  - <ul>
    - <ul> Markers

The Typography stylesheet provides styles for headings and inline elements

Includes <strong>, <em>, <code>, and <a> tags

Add the marker- attribute to a <ul> element to customize its list markers

Use the open keyword at the start and/or end of the marker- attribute to leave the top/bottom tree markers open

Apply typography styles to an element’s children

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/typography.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/typography.css';
```

Example 3 (jsx):
```jsx
<ul marker-="bullet">
    <ul marker-="tree"></ul>
</ul>
```

Example 4 (jsx):
```jsx
<ul marker-="bullet">
    <ul marker-="tree"></ul>
</ul>
```

---

## Table

**URL:** https://webtui.ironclad.sh/components/table

**Contents:**
- Table
- Import
- Usage
- Caveats
- Reference
  - Properties
  - Extending
  - Scope

The <caption> element absolutely obliterates the ascii box-like styling

To extend the Table stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/table.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/table.css';
```

Example 3 (typescript):
```typescript
<table>
    <thead>
        <tr>
            <td>...</td>
            <td>...</td>
            <td>...</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>...</td>
            <td>...</td>
            <td>...</td>
        </tr>
        ...
    </tbody>
</table>
```

Example 4 (typescript):
```typescript
<table>
    <thead>
        <tr>
            <td>...</td>
            <td>...</td>
            <td>...</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>...</td>
            <td>...</td>
            <td>...</td>
        </tr>
        ...
    </tbody>
</table>
```

---

## Radio

**URL:** https://webtui.ironclad.sh/components/radio

**Contents:**
- Radio
- Import
- Usage
- Examples
  - Checked/Unchecked
  - Disabled
- Reference
  - Extending
  - Scope

Displays a radio input styled like a terminal UI radio

To extend the Radio stylesheet, define a CSS rule on the components layer:

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/radio.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/radio.css';
```

Example 3 (jsx):
```jsx
<label>
    <input type="radio" name="choice" />
    Option
</label>
```

Example 4 (jsx):
```jsx
<label>
    <input type="radio" name="choice" />
    Option
</label>
```

---

## Input

**URL:** https://webtui.ironclad.sh/components/input

**Contents:**
- Input
- Import
- Usage
- Examples
  - Sizing
  - Box Borders
- Reference
  - Extending
  - Scope

The box- utility cannot be used directly on <input> elements because inputs don’t support pseudo-elements

Instead, wrap an <input> with a <label box-="*"> or use a contenteditable element

To extend the Input stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/input.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/input.css';
```

Example 3 (json):
```json
@layer components {
    input,
    [is-~='input'] {
        &[size-='thicc'] {
            height: 3lh;
            padding: 1lh 8ch;
        }

        /* ... */
    }
}
```

Example 4 (json):
```json
@layer components {
    input,
    [is-~='input'] {
        &[size-='thicc'] {
            height: 3lh;
            padding: 1lh 8ch;
        }

        /* ... */
    }
}
```

---

## Badge

**URL:** https://webtui.ironclad.sh/components/badge

**Contents:**
- Badge
- Import
- Usage
- Examples
  - Variants
  - Caps
- Reference
  - Properties
  - Extending
  - Scope

Use the variant- attribute to change the color of a badge

Available variants match the base theme colors

Use the cap- attribute to customize badges’ end cap styles

Pass two values separated by a space to customize the start and end cap styles

Use the following custom CSS properties to style badges

Using background-color directly will color behind the badge instead of coloring the badge shape

To extend the Badge stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/badge.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/badge.css';
```

Example 3 (jsx):
```jsx
<span is-="badge">badge</span>
```

Example 4 (jsx):
```jsx
<span is-="badge">badge</span>
```

---

## Textarea

**URL:** https://webtui.ironclad.sh/components/textarea

**Contents:**
- Textarea
- Import
- Usage
- Examples
  - Sizing
- Reference
  - Extending
  - Scope

To customize textarea styles, extend the component using @layer components:

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/textarea.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/textarea.css';
```

Example 3 (typescript):
```typescript
<textarea></textarea>
```

Example 4 (typescript):
```typescript
<textarea></textarea>
```

---

## Progress

**URL:** https://webtui.ironclad.sh/components/progress

**Contents:**
- Progress
- Import
- Usage
- Examples
  - Color
  - Text content
  - Hybrid
- Reference
  - Properties
  - Extending

Displays a progress bar

Note: The progress bar component automatically rounds the progress display value to the nearest character width

Unfortunately, due to gecko-based browsers not supporting modern attr() usage, you have to use CSS variables for controlling the progress value.

To dynamically update the progress value with JavaScript:

Important: Since CSS doesn’t have a way to arbitrarily repeat text a number of times, you will have to repeat a character an arbitrary amount of times for --progress-value-content or --progress-empty-content

To extend the Progress stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/progress.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/progress.css';
```

Example 3 (jsx):
```jsx
<div is-="progress" style="--progress-value: 50;">50%</div>
<div is-="progress" style="--progress-value: 2; --progress-max: 5;">2/5</div>
```

Example 4 (jsx):
```jsx
<div is-="progress" style="--progress-value: 50;">50%</div>
<div is-="progress" style="--progress-value: 2; --progress-max: 5;">2/5</div>
```

---

## View

**URL:** https://webtui.ironclad.sh/components/view

**Contents:**
- View
- Import
- Usage
- Examples
  - Basic usage
- Reference
  - Extending

Displays a TUI-accurate view that automatically rounds its width and height to the nearest ch and lh units respectively

Useful for building tiled UIs or a partial GUI/TUI application

To extend the View stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/view.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/view.css';
```

Example 3 (jsx):
```jsx
<div is-="view">
    <div is-="view-content">
        <!-- TUI contents -->
    </div>
</div>
```

Example 4 (jsx):
```jsx
<div is-="view">
    <div is-="view-content">
        <!-- TUI contents -->
    </div>
</div>
```

---

## Vitesse Theme

**URL:** https://webtui.ironclad.sh/plugins/theme-vitesse

**Contents:**
- Vitesse Theme
- Installation
- Variants
- Components
  - Typography
  - Badge
  - Button
- CSS Variables

A port of the Vitesse color palette to WebTUI.

Install the theme with your preferred package manager

Ensure you import the theme after all the other stylesheets from @webtui/css or the styles will not be applied.

Set the data-webtui-theme attribute on the <html> tag or a container element.

To only apply the theme to a specific element:

Supports dark and light modes, each with normal and soft contrast levels.

Components affected/modified by the theme:

Adds additional variants to badges matching Vitesse accent colors.

Adds additional variants to buttons matching Vitesse accent colors.

Adds the following CSS variables within the base layer, each of which change based on the theme variant

The base background/foreground colors use the following CSS variables from the Vitesse palette

**Examples:**

Example 1 (python):
```python
bun i @webtui/theme-vitesse
npm i @webtui/theme-vitesse
yarn add @webtui/theme-vitesse
pnpm i @webtui/theme-vitesse
```

Example 2 (python):
```python
bun i @webtui/theme-vitesse
npm i @webtui/theme-vitesse
yarn add @webtui/theme-vitesse
pnpm i @webtui/theme-vitesse
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-vitesse';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-vitesse';
```

---

## Dialog

**URL:** https://webtui.ironclad.sh/components/dialog

**Contents:**
- Dialog
- Import
- Usage
- Examples
  - Positioning
  - Container
  - Sizing
  - Offset
- Reference
  - Properties

See the MDN Reference on how to show and hide <dialog> elements

Use the position- property to control the position of the dialog

Pass two values into position- to set the horizontal and vertical position anchor

Use the container- property to control the container sizing behavior

Use the size- property to control the max size of the dialog

Use the --dialog-offset-x and --dialog-offset-y CSS properties to control the offset of the dialog

The --dialog-max-width and --dialog-max-height CSS properties are automatically controlled by the size- variant but can be overridden

To extend the Dialog stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/dialog.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/dialog.css';
```

Example 3 (typescript):
```typescript
<dialog>
    <div>Dialog content</div>
</dialog>
```

Example 4 (typescript):
```typescript
<dialog>
    <div>Dialog content</div>
</dialog>
```

---

## Catppuccin Theme

**URL:** https://webtui.ironclad.sh/plugins/theme-catppuccin

**Contents:**
- Catppuccin Theme
- Installation
- Flavors
- Components
  - Typography
  - Badge
  - Button
- CSS Variables

A port of the Catppuccin color palette to WebTUI

Install the theme with your preferred package manager

Ensure you import the theme after all the other stylesheets from @webtui/css or the styles will not be applied

Set the data-webtui-theme attribute to the <html> tag

To only apply the theme to a specific element, use the same attribute

Supports all four Catppuccin theme flavors.

Components affected/modified by the theme

Adds additional variants to badges matching all custom accent colors

Adds additional variants to buttons matching all custom accent colors

Adds the following CSS variables to the base layer

The base background/foreground colors use the following CSS variables from the Catppuccin palette

**Examples:**

Example 1 (python):
```python
bun i @webtui/theme-catppuccin
npm i @webtui/theme-catppuccin
yarn add @webtui/theme-catppuccin
pnpm i @webtui/theme-catppuccin
```

Example 2 (python):
```python
bun i @webtui/theme-catppuccin
npm i @webtui/theme-catppuccin
yarn add @webtui/theme-catppuccin
pnpm i @webtui/theme-catppuccin
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-catppuccin';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-catppuccin';
```

---

## Spinner

**URL:** https://webtui.ironclad.sh/components/spinner

**Contents:**
- Spinner
- Import
- Usage
- Examples
  - Variants
  - Speed
  - Direction
- Reference
  - Properties
  - Extending

To extend the Spinner stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/spinner.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/spinner.css';
```

Example 3 (jsx):
```jsx
<span is-="spinner"></span>
```

Example 4 (jsx):
```jsx
<span is-="spinner"></span>
```

---

## Nord Theme

**URL:** https://webtui.ironclad.sh/plugins/theme-nord

**Contents:**
- Nord Theme
- Installation
- Components
  - Typography
  - Badge
  - Button
- CSS Variables

A port of the Nord color palette to WebTUI

Install the theme with your preferred package manager

Ensure you import the theme after all the other stylesheets from @webtui/css

Set the data-webtui-theme attribute to the <html> tag

To only apply the theme to a specific element, use the same attribute

Components affected/modified by the theme

Adds additional variants to badges matching all custom accent colors

Adds additional variants to buttons matching all custom accent colors

Adds the following CSS variables to the base layer

The base background/foreground colors use the following CSS variables from the Nord palette

**Examples:**

Example 1 (python):
```python
bun i @webtui/theme-nord
npm i @webtui/theme-nord
yarn add @webtui/theme-nord
pnpm i @webtui/theme-nord
```

Example 2 (python):
```python
bun i @webtui/theme-nord
npm i @webtui/theme-nord
yarn add @webtui/theme-nord
pnpm i @webtui/theme-nord
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-nord';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
@import '@webtui/css/components/typography.css';
/* ... */

@import '@webtui/theme-nord';
```

---

## Tooltip

**URL:** https://webtui.ironclad.sh/components/tooltip

**Contents:**
- Tooltip
- Import
- Usage
- Examples
  - Positioning
- Reference
  - Properties
  - Extending
  - Scope

Displays information in a popup when a specified trigger is focused or hovered over

Use baseline-* values to position the content relative to the edges of the tooltip

This image from the Popover page shows the values and positions that can be used in the position- property

To extend the Tooltip stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/tooltip.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/tooltip.css';
```

Example 3 (jsx):
```jsx
<div is-="tooltip">
    <div is-="tooltip-trigger">Tooltip trigger</div>
    <div is-="tooltip-content">
        <p>Tooltip content</p>
    </div>
</div>
```

Example 4 (jsx):
```jsx
<div is-="tooltip">
    <div is-="tooltip-trigger">Tooltip trigger</div>
    <div is-="tooltip-content">
        <p>Tooltip content</p>
    </div>
</div>
```

---

## Separator

**URL:** https://webtui.ironclad.sh/components/separator

**Contents:**
- Separator
- Import
- Usage
- Examples
  - Vertical
  - Caps
    - Bisecting Caps
    - Edge Caps
    - Start and End Caps
- Reference

A horizontal or vertical separator

If direction- is not specified, separators are horizontal by default.

Use the cap- attribute to control how the ends of the separator behave

cap-="bisect" extends the edges of the separator by 0.5ch making it ideal to use alongside the box- utility

cap-="edge" clips off the edges of the separator by 0.5ch making it ideal for corners

You can specify two values for the cap- attribute to control the start and end of the separator

Use the following custom CSS properties to style separators

To extend the Separator stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/separator.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/separator.css';
```

Example 3 (jsx):
```jsx
<!-- Horizontal -->
<div is-="separator"></div>
<div is-="separator" direction-="horizontal"></div>
<div is-="separator" direction-="x"></div>

<!-- Vertical -->
<div is-="separator" direction-="vertical"></div>
<div is-="separator" direction-="y"></div>
```

Example 4 (jsx):
```jsx
<!-- Horizontal -->
<div is-="separator"></div>
<div is-="separator" direction-="horizontal"></div>
<div is-="separator" direction-="x"></div>

<!-- Vertical -->
<div is-="separator" direction-="vertical"></div>
<div is-="separator" direction-="y"></div>
```

---

## Popover

**URL:** https://webtui.ironclad.sh/components/popover

**Contents:**
- Popover
- Import
- Usage
- Examples
  - Positioning
  - Backdrop
- Caveats
- Reference
  - Properties
  - Extending

Creates a popover component powered by the Details and Summary elements

Use the position- property to control the position of the popover

Pass two values into position- to set the horizontal and vertical position anchor

Use baseline-* values to position the content relative to the edges of the popover

The image below shows the values and positions that can be used in the position- property

Elements using the box- utility that appear after the popover in the html markup will appear above the popover content no matter what z-index you provide

As a workaround, you can set the flex-direction to row-reverse or column-reverse on the parent element of the popover

To extend the Popover stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/popover.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/popover.css';
```

Example 3 (jsx):
```jsx
<details is-="popover">
    <summary>Popover</summary>
    <div>Popover content</div>
</details>
```

Example 4 (jsx):
```jsx
<details is-="popover">
    <summary>Popover</summary>
    <div>Popover content</div>
</details>
```

---

## Range

**URL:** https://webtui.ironclad.sh/components/range

**Contents:**
- Range
- Import
- Usage
- Examples
  - Basic Range
  - Track Styles
  - With Labels
  - Disabled State
- Reference
  - Properties

Use CSS custom properties to customize the range appearance

To extend the Range stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/range.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/range.css';
```

Example 3 (jsx):
```jsx
<input type="range" />
```

Example 4 (jsx):
```jsx
<input type="range" />
```

---

## Pre

**URL:** https://webtui.ironclad.sh/components/pre

**Contents:**
- Pre
- Import
- Usage
- Examples
  - Sizing
- Reference
  - Properties
  - Extending
  - Scope

Displays preformatted text

To extend the Pre stylesheet, define a CSS rule on the components layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/components/pre.css';
```

Example 2 (python):
```python
@import '@webtui/css/components/pre.css';
```

Example 3 (typescript):
```typescript
<pre>This is preformatted text</pre>
```

Example 4 (typescript):
```typescript
<pre>This is preformatted text</pre>
```

---
