---
name: ink
description: Ink (React for CLIs) and Ink-UI component library. Use for building interactive command-line applications with React components, flexbox terminal layouts, keyboard input handling, and pre-built UI components like spinners, inputs, and selects.
---

# Ink (Unified)

Ink (React for CLIs) and Ink-UI component library. Use for building interactive command-line applications with React components, flexbox terminal layouts, keyboard input handling, and pre-built UI components like spinners, inputs, and selects.

## When to Use This Skill

This skill should be triggered when:
- Building interactive CLI applications with React components
- Working with Ink's Flexbox layout system
- Using Ink hooks (useInput, useApp, useFocus, etc.)
- Styling terminal output with colors and formatting
- Using Ink-UI pre-built components (inputs, selects, spinners)
- Rendering and controlling Ink app lifecycle
- Debugging Ink applications

## Quick Reference

### Common Patterns

**Basic Ink App:**
```tsx
import React from 'react';
import { render, Box, Text } from 'ink';

function App() {
  return (
    <Box flexDirection="column">
      <Text>Hello World</Text>
    </Box>
  );
}

render(<App />);
```

**Flexbox Layout:**
```tsx
<Box flexDirection="row" alignItems="center" gap={2}>
  <Text color="cyan">Item 1</Text>
  <Text color="green">Item 2</Text>
</Box>
```

**Keyboard Input:**
```tsx
import { useInput } from 'ink';

function App() {
  useInput((input, key) => {
    if (key.return) {
      // Handle Enter key
    }
  });

  return <Text>Press Enter</Text>;
}
```

**Text Styling:**
```tsx
<Text color="red" bold italic>
  Styled text
</Text>

<Text backgroundColor="blue" color="white">
  With background
</Text>
```

**Ink-UI Text Input:**
```tsx
import { TextInput } from '@inkjs/ui';

function SearchForm() {
  const [value, setValue] = React.useState('');

  return (
    <TextInput
      placeholder="Search..."
      value={value}
      onChange={setValue}
    />
  );
}
```

**Ink-UI Select:**
```tsx
import { Select } from '@inkjs/ui';

function ColorPicker() {
  const [selected, setSelected] = React.useState();

  return (
    <Select
      options={[
        { label: 'Red', value: 'red' },
        { label: 'Green', value: 'green' },
      ]}
      onChange={setSelected}
    />
  );
}
```

**Ink-UI Spinner:**
```tsx
import { Spinner } from '@inkjs/ui';

function Loading() {
  return <Spinner label="Loading..." />;
}
```

**Ink-UI Progress Bar:**
```tsx
import { ProgressBar } from '@inkjs/ui';

function DownloadProgress() {
  return <ProgressBar value={45} />;
}
```

## Ink Core Components

### `<Box>`
Flexbox container (equivalent to `<div>` in browser React).

**Key Props:**
- `flexDirection` - Layout direction ('row', 'column')
- `flexGrow`, `flexShrink`, `flexBasis` - Flex properties
- `alignItems` - Cross-axis alignment ('flex-start', 'center', 'flex-end')
- `justifyContent` - Main-axis alignment ('flex-start', 'center', 'flex-end', 'space-between')
- `width`, `height`, `minWidth`, `minHeight` - Dimensions
- `padding`, `paddingX`, `paddingY` - Spacing
- `margin`, `marginX`, `marginY` - Spacing
- `gap` - Gap between flex items
- `borderStyle` - Border ('single', 'double', 'round', 'bold', etc.)
- `borderColor` - Border color
- `backgroundColor` - Background color

### `<Text>`
Text output component - all text must be wrapped in `<Text>`.

**Key Props:**
- `color` - Text color (chalk colors or hex)
- `backgroundColor` - Background color
- `bold` - Make bold (boolean)
- `italic` - Make italic (boolean)
- `underline` - Make underlined (boolean)
- `strikethrough` - Add strikethrough (boolean)
- `dimColor` - Dim color (boolean)
- `wrap` - Text wrapping ('wrap', 'truncate', 'truncate-start', 'truncate-middle', 'truncate-end')

**Colors:** Use chalk color names (red, green, blue, cyan, magenta, yellow, white, gray) or hex values (#FF5733) or rgb(r,g,b).

### `<Newline>`
Insert line breaks.

**Props:**
- `count` - Number of newlines (default: 1)

### `<Transform>`
Transform child text output.

**Props:**
- `transform` - Function `(text: string, index: number) => string`

### `<Static>`
Render content once at top of output (for logs, completed tasks).

**Props:**
- `items` - Array of static items
- `style` - Style for items

**Note:** Items array should only grow - modifications to existing items are ignored.

## Ink Hooks

### `useInput`
Handle keyboard input.

```tsx
useInput((input: string, key: Key) => void, options?: {isActive?: boolean})
```

**Key object properties:**
- `leftArrow`, `rightArrow`, `upArrow`, `downArrow` - Arrow keys
- `return` - Enter key
- `escape` - Escape key
- `ctrl`, `shift` - Modifier keys
- `tab` - Tab key
- `backspace`, `delete` - Delete keys
- `meta` - Command/Windows key

**Note:** Use `isActive` prop to control when input is active. Only one `useInput` should be active at a time.

### `useApp`
Access app instance.

```tsx
const { exit } = useApp();
```

**Returns:**
- `exit(error?: Error) => void` - Exit app

### `useFocus`
Make component focusable.

```tsx
const { isFocused, focus } = useFocus(options?)
```

**Options:**
- `autoFocus?: boolean` - Auto-focus on mount
- `id?: string` - Component ID for programmatic focus
- `isActive?: boolean` - Enable/disable focus

### `useFocusManager`
Programmatic focus control.

```tsx
const {
  focusNext,
  focusPrevious,
  focus,
  enableFocus,
  disableFocus
} = useFocusManager();
```

### `useStdin`
Access stdin stream.

```tsx
const { stdin, setRawMode, isRawModeSupported } = useStdin();
```

### `useStdout`
Access stdout stream.

```tsx
const { stdout, write } = useStdout();
```

**Note:** `write()` outputs above Ink's rendering area.

### `useStderr`
Access stderr stream.

```tsx
const { stderr, write } = useStderr();
```

## Ink-UI Components

### Text Input Components

**`<TextInput>`** - Single-line text input with optional autocomplete
- `isDisabled`, `placeholder`, `defaultValue`, `suggestions`, `onChange`, `onSubmit`

**`<PasswordInput>`** - Text input with masked characters
- `isDisabled`, `placeholder`, `onChange`, `onSubmit`

**`<EmailInput>`** - Text input with email domain autocomplete
- `isDisabled`, `placeholder`, `defaultValue`, `domains`, `onChange`, `onSubmit`

**`<ConfirmInput>`** - Y/N confirmation prompt
- `isDisabled`, `defaultChoice`, `submitOnEnter`, `onConfirm`, `onCancel`

### Selection Components

**`<Select>`** - Single-select dropdown
- `isDisabled`, `visibleOptionCount`, `highlightText`, `options`, `defaultValue`, `onChange`
- **Options shape:** `Array<{label: string, value: string}>`

**`<MultiSelect>`** - Multi-select with checkboxes
- `isDisabled`, `visibleOptionCount`, `highlightText`, `options`, `defaultValue`, `onChange`
- **Returns:** `string[]` (array of selected values)

### Feedback Components

**`<Spinner>`** - Animated loading indicator
- `label` - Loading label text

**`<ProgressBar>`** - Progress indicator (value 0-100)
- `value` - Progress value (0-100)

**`<StatusMessage>`** - Styled status message
- `variant` - Message type ('info', 'success', 'error', 'warning')

**`<Alert>`** - Alert box with optional title
- `children`, `variant`, `title`
- **Variants:** 'info', 'success', 'error', 'warning'

**`<Badge>`** - Colored badge/tag
- `children`, `color`
- **Colors:** green, red, yellow, blue

### List Components

**`<UnorderedList>`** - Bulleted list with `UnorderedList.Item` children
- Supports nesting

**`<OrderedList>`** - Numbered list with `OrderedList.Item` children
- Supports nesting

### Theming

**`<ThemeProvider>`** - Wrap app to apply custom theme
- Provides theme context to all child components

**`defaultTheme`** - Default Ink-UI theme object

**`extendTheme()`** - `extendTheme(defaultTheme, customizations)` - Merge custom styles

**`useTheme()`** - Access current theme in components

## Rendering

### `render()`
Start rendering Ink app.

```tsx
const { waitUntilExit } = render(<App />);
```

**Options:**
- `stdout` - Output stream (default: process.stdout)
- `stdin` - Input stream (default: process.stdin)
- `exitOnCtrlC` - Exit on Ctrl+C (default: true)
- `patchConsole` - Intercept console.log (default: true)
- `debug` - Debug mode
- `maxFps` - Max renders per second (default: 30)

### Instance Methods

- `waitUntilExit()` - Wait for app to exit (Promise)
- `unmount()` - Unmount and clear output
- `clear()` - Clear Ink output but preserve terminal
- `rerender(element)` - Force rerender with new element

## CLI Development Gotchas

### Terminal Constraints
- **Finite dimensions:** Use `useStdout().stdout.columns/rows` or `measureElement()`
- **No scrolling:** Content exceeding terminal height is lost unless using `Static` component
- **Colors:** Depend on terminal support - test with `FORCE_COLOR=0` for no-color fallback
- **Unicode/emoji:** Rendering varies by terminal - test across iTerm2, Terminal.app, Windows Terminal, VS Code terminal
- **Box-drawing:** Characters may not render in all terminals or fonts
- **Text wrapping:** Character-based by default - use `wrap='wrap'` for word wrap
- **Resize events:** Require handling - components don't auto-resize without explicit `useStdout` hooks

### Input Handling
- **Raw mode required:** Terminal must support character-by-character input
- **stdin is shared:** Only one `useInput` should be active at a time (use `isActive` prop)
- **Ctrl+C handling:** `exitOnCtrlC=true` (default) exits immediately; set false to handle manually
- **Paste events:** Come as single input with full text, not character-by-character
- **Special keys:** Arrow keys, function keys require special key object checks
- **Tab navigation:** Reserved for focus navigation when using `useFocus` system

### Rendering
- **No DOM diffing:** Ink re-renders entire UI on each state change (unlike browser React)
- **console.log():** Interferes with rendering - use `patchConsole:true` or `useStdout().write()`
- **Static items:** Immutable after render - only new items trigger re-render
- **No CSS:** All styling via props (color, bold, etc.) using chalk color names or hex
- **Percentage widths:** Only work when parent has explicit dimensions
- **measureElement():** Only works after initial render - call in `useEffect`, not during render

### Lifecycle
- **useApp().exit():** Async - await it or handle cleanup in `waitUntilExit()`
- **unmount():** Clears terminal output - use `clear()` to preserve static content
- **Multiple render() calls:** Create multiple instances - usually you want one
- **stdin cleanup:** Manual cleanup needed if app crashes - terminal left in raw mode
- **Process exit:** Doesn't exit automatically - call `process.exit()` or use `exit()` from `useApp`

## Library Opinions (Built-in Constraints)

- **Flexbox only:** All layout uses Yoga flexbox - no CSS Grid, no absolute positioning, no floats
- **No DOM:** Cannot use `document.*`, `window.*`, or any browser APIs
- **Text in <Text>:** All text content MUST be wrapped in `<Text>` component - bare strings cause errors
- **Single-line default:** Text doesn't wrap by default - explicitly set `wrap='wrap'` or `'truncate'`
- **No images:** Terminal is text-only (use ASCII art or Unicode block characters)
- **No custom fonts:** Font is terminal's monospace - no font-family, font-size
- **Sync rendering:** Blocking - long operations freeze UI
- **No animations API:** Use `setInterval/setTimeout` with state updates
- **stdout only:** Output goes to stdout only - stderr requires `useStderr` hook
- **Sequential focus:** Linear navigation (Tab order) - no 2D focus grid navigation

## Reference Files

Comprehensive documentation in `references/`:
- **other.md** - Ink Core documentation (components, hooks, rendering, styling)
- **selection.md** - Ink-UI selection components (Select, MultiSelect, ConfirmInput)
- **feedback.md** - Ink-UI feedback components (Spinner, ProgressBar, StatusMessage, Alert, Badge)
- **index.md** - Index of all reference files

Use `view` to read specific reference files when detailed information is needed.

## Working with This Skill

### For Beginners
Start with `other.md` for Ink Core fundamentals, then explore Ink-UI components.

### For Ink Core
Refer to `other.md` for:
- Core components (Box, Text, Newline, Transform, Static)
- Hooks (useInput, useApp, useFocus, etc.)
- Rendering and layout
- Styling and colors

### For Ink-UI Components
Refer to category files:
- `selection.md` - Inputs and selection controls (TextInput, Select, MultiSelect)
- `feedback.md` - Feedback components (Spinner, ProgressBar, Alert, Badge)

### For Code Examples
The quick reference section above contains common patterns extracted from official docs.

## Resources

### references/
Organized documentation extracted from official sources:
- **Ink Core:** https://github.com/vadimdemedes/ink
- **Ink-UI:** https://github.com/vadimdemedes/ink-ui

Reference files contain:
- Detailed explanations
- Code examples with language annotations
- Links to original documentation
- Table of contents for quick navigation

### scripts/
Add helper scripts for common automation tasks.

### assets/
Add templates, boilerplate, or example projects here.

## Notes

- This skill combines both Ink Core and Ink-UI libraries
- Reference files preserve structure and examples from source docs
- Code examples include language detection for better syntax highlighting
- Quick reference patterns are extracted from common usage examples in docs

## Updating

To refresh this skill with updated documentation:
1. Re-run unified scraper with same configuration
2. The skill will be rebuilt with latest information
