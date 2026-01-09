# Webtui_Docs - Getting Started

**Pages:** 6

---

## TUIs vs GUIs

**URL:** https://webtui.ironclad.sh/start/tuis-vs-guis

**Contents:**
- TUIs vs GUIs
- Monospace Fonts
- Character Cells

Terminal UIs (TUIs) use ASCII/Unicode characters to represent UI elements

Graphical UIs (GUIs) instead use graphical elements like windows, buttons, inputs, etc

To build Terminal-like UIs, you will need to

In most traditional fonts, some characters (like i) are narrower than others (like w)

All characters in Monospace Fonts occupy the same width

This makes it easier to align characters

It also makes it easier to create ASCII art

Stop thinking in standard CSS units like px, em, rem, %

Start thinking in Character Cells for spacing, sizing, and positioning

CSS comes with two units that represent the width and height of a single character cell:

Remember that when using a Monospace font, every character has the same width, so ch applies for all characters

If I were to create a box that is 10 characters wide and 5 lines tall, I would use the following CSS:

Now that you understand the differences between building TUIs and GUIs, let’s dive in

**Examples:**

Example 1 (python):
```python
_nnnn_
        dGGGGMMb
       @p~qp~~qMb
       M|@||@) M|
       @,----.JM|
      JS^\__/  qKL
     dZP        qKRb
    dZP          qKKb
   fZP            SMMb
   HZM            MMMM
   FqM            MMMM
 __| ".        |\dS"qML
 |    `.       | `' \Zq
_)      \.___.,|     .'
\____   )MMMMMP|   .'
     `-'       `--' hjm
```

Example 2 (python):
```python
_nnnn_
        dGGGGMMb
       @p~qp~~qMb
       M|@||@) M|
       @,----.JM|
      JS^\__/  qKL
     dZP        qKRb
    dZP          qKKb
   fZP            SMMb
   HZM            MMMM
   FqM            MMMM
 __| ".        |\dS"qML
 |    `.       | `' \Zq
_)      \.___.,|     .'
\____   )MMMMMP|   .'
     `-'       `--' hjm
```

Example 3 (css):
```css
.box {
    width: 10ch;
    height: 5lh;
}
```

Example 4 (css):
```css
.box {
    width: 10ch;
    height: 5lh;
}
```

---

## Installation

**URL:** https://webtui.ironclad.sh/start/installation

**Contents:**
- Installation
- Installation
- Using CSS
- Using ESM
- Using a CDN
- Full Library Import
  - CSS
  - ESM
  - CDN

This guide covers how to install WebTUI with CSS, ESM, and CDN imports

For framework-specific guides, check out the Installation section

Install the base WebTUI package with your preferred package manager

Define the order of layers in your global CSS file using the @layer at-rule [MDN Reference]

Ensure you define the layer order before importing the base stylesheet

Import the desired utilities, components, and themes after the base stylesheet

If you aren’t using a bundler, see Using a CDN instead

Stylesheets can be imported via ESM if your bundler supports it

Ensure you have previously defined the order of layers in your global CSS file

If you aren’t using a bundler, you can import the CSS files from a CDN like JSDelivr

Browse all the available CSS files on the JSDelivr page

Individual css module imports are preferred in production to avoid importing unused CSS

For development, you can import the entire library

Import the library using the direct package name after defining the order of layers

If you aren’t using a bundler, see CDN

Import the library using the direct package name

Import the /dist/full.css path instead of the base path

Optionally, you can use a <link> tag

**Examples:**

Example 1 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 2 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

---

## Astro

**URL:** https://webtui.ironclad.sh/installation/astro

**Contents:**
- Astro
- Scoping
  - Frontmatter Imports
  - <style> tag
  - Full Library Import

Install the base WebTUI package with your preferred package manager

Define the order of layers and import the base stylesheet in your global CSS file

Import additional utilities, components, and themes the CSS file

Import the global CSS file in your root layout file in the frontmatter section

Astro scopes styles to specific islands instead of globally

Import the desired file in the frontmatter section of your component

Astro scopes CSS in <style> tags to specific components instead of globally [docs]

Add the is:global modifier to the <style> tag if you intend to import a CSS file from WebTUI

Modify astro.config.mjs and enable noExternal for @webtui/css if you intend to import the Full Library [docs]

**Examples:**

Example 1 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 2 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

---

## Introduction

**URL:** https://webtui.ironclad.sh/start/intro

**Contents:**
- Introduction
- Features

WebTUI is a CSS library that brings the beauty of Terminal User Interfaces (TUIs) to the browser

---

## Next.js

**URL:** https://webtui.ironclad.sh/installation/nextjs

**Contents:**
- Next.js

Install the base WebTUI package with your preferred package manager

Define the order of layers and import the base stylesheet in globals.css

Import additional utilities, components, and themes in globals.css

ESM imports can be used to scope styles to specific components

**Examples:**

Example 1 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 2 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

---

## Vite

**URL:** https://webtui.ironclad.sh/installation/vite

**Contents:**
- Vite

Install the base WebTUI package with your preferred package manager

Define the order of layers and import the base stylesheet in your global CSS file

Import additional utilities, components, and themes in globals.css

ESM imports can be used to scope styles to specific files

**Examples:**

Example 1 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 2 (python):
```python
bun i @webtui/css
npm i @webtui/css
yarn add @webtui/css
pnpm install @webtui/css
```

Example 3 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

Example 4 (python):
```python
@layer base, utils, components;

@import '@webtui/css/base.css';
```

---
