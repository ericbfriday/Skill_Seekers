# Webtui_Docs - Utilities

**Pages:** 1

---

## ASCII Boxes

**URL:** https://webtui.ironclad.sh/start/ascii-boxes

**Contents:**
- ASCII Boxes
- Import
- Usage
- Examples
  - Border Types
  - Shearing
- Reference
  - Properties
  - Extending

The box- utility utilizes CSS ::before and ::after pseudo-elements to mimic a boxes drawn with ASCII Box-drawing characters

Important: The box- utility is suffixed with a -

Using box="..." will not work

Shearing off top/bottom padding allows overlaying content over the top and/or bottom edges of the box

To extend the Box stylesheet, define a CSS rule on the utils layer

**Examples:**

Example 1 (python):
```python
@import '@webtui/css/utils/box.css';
```

Example 2 (python):
```python
@import '@webtui/css/utils/box.css';
```

Example 3 (jsx):
```jsx
<div box-="square">Hi Mom</div>
```

Example 4 (jsx):
```jsx
<div box-="square">Hi Mom</div>
```

---
