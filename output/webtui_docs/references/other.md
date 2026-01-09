# Webtui_Docs - Other

**Pages:** 2

---

## Changelog

**URL:** https://webtui.ironclad.sh/start/changelog

**Contents:**
- Changelog
- 0.1.6
  - Migration Guide from 0.1.5
- 0.1.5
- 0.1.4
- 0.1.3
- 0.1.2
  - Migration Guide from < 0.1.1
    - Heading Markers
    - Inherited Color Variables

You can no longer use the <progress> HTML element to display a styled progress bar due to incompatibilities with Gecko-based browsers. Use something like a <div> that supports CSS pseudo-elements.

Additionally, you can no longer use the value and max attributes to control the progress bar. Use the --progress-value and --progress-max instead.

To control the progress bar dynamically with JavaScript:

The markdown-like # markers on heading elements <h1>-<h6> have been removed

If you intend to keep this behavior, use the following CSS code

Prior to this release, --box-border-color, --table-border-color, and --separator-color always defaulted to --foreground0.

Now all child elements of an element with any of these CSS variables will inherit the value from its closest ancestor

The defaults for these variables can be controlled from :root

The default size for pre elements has been changed to make them shorter

In versions prior to 0.1.0, a second keyword was added to the box- utility property to control whether the box should pad the top and/or bottom lines

This has been replaced with the shear- property

Since the selector for box- no longer allows more than one keyword, most elements using the box- utility will be affected by this change

The default button variant has been changed to make the button shorter

The default button variant is still three lines tall but appears to be shorter as its background doesn’t take up the full height of three lines

**Examples:**

Example 1 (jsx):
```jsx
<div is-="progress" style="--progress-value: 50;">50%</div>
<div is-="progress" style="--progress-value: 8; --progress-max: 10;">8/10</div>
```

Example 2 (jsx):
```jsx
<div is-="progress" style="--progress-value: 50;">50%</div>
<div is-="progress" style="--progress-value: 8; --progress-max: 10;">8/10</div>
```

Example 3 (unknown):
```unknown
document
    .getElementById('my-progress-element')
    .style.setProperty('--progress-max', '100')
```

Example 4 (unknown):
```unknown
document
    .getElementById('my-progress-element')
    .style.setProperty('--progress-max', '100')
```

---

##  Terminal UIs for the  Web

**URL:** https://webtui.ironclad.sh/

**Contents:**
-  Terminal UIs for the  Web
- Semantic Approach
- Pure, Modular CSS
-  Easily Themable
-  Gallery

WebTUI is a modular CSS library that brings the beauty of Terminal UIs to the browser

HTML tags and attributes determine the behavior and appearance of different components

The dash at the end of each attribute name (-), makes WebTUI compatible with frameworks like React

WebTUI is built with Pure CSS and does not require any JavaScript

Import and use only what you need

Install beautiful pre-built color themes or create your own

Can anyone point me to some code to determine if a number in JavaScript is even or odd?

Awesome projects built with WebTUI

WebTUI can be supported via Github Sponsors

Thanks to all our generous supporters, including:

Modular CSS Library that brings the beauty of Terminal UIs to the browser

---
