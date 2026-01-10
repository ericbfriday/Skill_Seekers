---
name: ink-ui
description: Ink-UI component library. Pre-built UI components for Ink CLIs: text inputs, select dropdowns, spinners, progress bars, alerts, and theming support.
---

# Ink-UI Skill

Ink-UI component library - Pre-built UI components for Ink CLIs: text inputs, select dropdowns, spinners, progress bars, alerts, and theming support.

## When to Use This Skill

This skill should be triggered when:
- Using pre-built UI components from Ink-UI library
- Implementing text inputs in Ink applications
- Creating selection controls (dropdowns, multi-selects)
- Adding feedback components (spinners, progress bars, alerts)
- Implementing theming for Ink apps
- Working with lists (ordered, unordered)

## Quick Reference

### Common Patterns

**Text Input:**
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

**Select Dropdown:**
```tsx
import { Select } from '@inkjs/ui';

function ColorPicker() {
  const [selected, setSelected] = React.useState();

  return (
    <Select
      options={[
        { label: 'Red', value: 'red' },
        { label: 'Green', value: 'green' },
        { label: 'Blue', value: 'blue' },
      ]}
      onChange={setSelected}
    />
  );
}
```

**Spinner (Loading):**
```tsx
import { Spinner } from '@inkjs/ui';

function Loading() {
  return <Spinner label="Loading..." />;
}
```

**Progress Bar:**
```tsx
import { ProgressBar } from '@inkjs/ui';

function DownloadProgress() {
  return <ProgressBar value={45} />;
}
```

**Alert:**
```tsx
import { Alert } from '@inkjs/ui';

function SuccessMessage() {
  return (
    <Alert variant="success">
      Operation completed successfully!
    </Alert>
  );
}
```

## Text Input Components

### `<TextInput>`
Single-line text input with optional autocomplete.

**Props:**
- `isDisabled?: boolean` - Disable input
- `placeholder?: string` - Placeholder text
- `defaultValue?: string` - Initial value
- `suggestions?: string[]` - Autocomplete suggestions
- `onChange?: (value: string) => void` - Change callback
- `onSubmit?: (value: string) => void` - Submit callback (Enter)

### `<PasswordInput>`
Text input with masked characters.

**Props:**
- `isDisabled?: boolean` - Disable input
- `placeholder?: string` - Placeholder text
- `onChange?: (value: string) => void` - Change callback
- `onSubmit?: (value: string) => void` - Submit callback (Enter)

### `<EmailInput>`
Text input with email domain autocomplete.

**Props:**
- `isDisabled?: boolean` - Disable input
- `placeholder?: string` - Placeholder text
- `defaultValue?: string` - Initial value
- `domains?: string[]` - Custom domain list (default: common email providers)
- `onChange?: (value: string) => void` - Change callback
- `onSubmit?: (value: string) => void` - Submit callback (Enter)

**Default domains:** aol.com, gmail.com, yahoo.com, hotmail.com, live.com, outlook.com, icloud.com, hey.com

### `<ConfirmInput>`
Y/N confirmation prompt.

**Props:**
- `isDisabled?: boolean` - Disable input
- `defaultChoice?: 'confirm' | 'cancel'` - Default selection
- `submitOnEnter?: boolean` - Submit on Enter (default: true)
- `onConfirm?: () => void` - Confirm callback
- `onCancel?: () => void` - Cancel callback

**Default choices:** 'confirm', 'cancel'

## Selection Components

### `<Select>`
Single-select dropdown.

**Props:**
- `isDisabled?: boolean` - Disable selection
- `visibleOptionCount?: number` - Visible options (default: 8)
- `highlightText?: boolean` - Highlight matches in list
- `options?: Array<{label: string, value: string}>` - Options
- `defaultValue?: string` - Initial selected value
- `onChange?: (value: string) => void` - Selection callback

### `<MultiSelect>`
Multi-select with checkboxes.

**Props:**
- `isDisabled?: boolean` - Disable selection
- `visibleOptionCount?: number` - Visible options (default: 8)
- `highlightText?: boolean` - Highlight matches in list
- `options?: Array<{label: string, value: string}>` - Options
- `defaultValue?: string[]` - Initial selected values
- `onChange?: (values: string[]) => void` - Selection callback

**Returns:** Array of selected values (string[])

## Feedback Components

### `<Spinner>`
Animated loading indicator.

**Props:**
- `label?: string` - Loading label text

### `<ProgressBar>`
Progress indicator (0-100).

**Props:**
- `value: number` - Progress value (0-100)

### `<StatusMessage>`
Styled status message.

**Props:**
- `variant?: 'info' | 'success' | 'error' | 'warning'` - Message type

### `<Alert>`
Alert box with optional title.

**Props:**
- `children?: ReactNode` - Alert content
- `variant?: 'info' | 'success' | 'error' | 'warning'` - Alert type
- `title?: string` - Alert title

**Variants:** 'info', 'success', 'error', 'warning'

### `<Badge>`
Colored badge/tag.

**Props:**
- `children: ReactNode` - Badge content
- `color?: 'green' | 'red' | 'yellow' | 'blue'` - Badge color

**Colors:** green, red, yellow, blue

## List Components

### `<UnorderedList>`
Bulleted list with `UnorderedList.Item` children.

**Features:**
- Supports nesting (recursive lists)
- Custom bullet styles

```tsx
<UnorderedList>
  <UnorderedList.Item>First item</UnorderedList.Item>
  <UnorderedList.Item>Second item</UnorderedList.Item>
  <UnorderedList>
    <UnorderedList.Item>Nested item 1</UnorderedList.Item>
    <UnorderedList.Item>Nested item 2</UnorderedList.Item>
  </UnorderedList>
</UnorderedList>
```

### `<OrderedList>`
Numbered list with `OrderedList.Item` children.

**Features:**
- Supports nesting (recursive lists)
- Automatic numbering

```tsx
<OrderedList>
  <OrderedList.Item>First step</OrderedList.Item>
  <OrderedList.Item>Second step</OrderedList.Item>
  <OrderedList>
    <OrderedList.Item>Sub-step 1</OrderedList.Item>
    <OrderedList.Item>Sub-step 2</OrderedList.Item>
  </OrderedList>
</OrderedList>
```

## Theming

### `ThemeProvider`
Wrap app to apply custom theme.

```tsx
import { ThemeProvider } from '@inkjs/ui';

function App() {
  return (
    <ThemeProvider theme={customTheme}>
      <YourApp />
    </ThemeProvider>
  );
}
```

### `defaultTheme`
Default Ink-UI theme object.

### `extendTheme`
Merge custom styles with default theme.

```tsx
const customTheme = extendTheme(defaultTheme, {
  components: {
    Spinner: {
      styles: {
        frame: () => ({ color: 'magenta' }),
      },
    },
  },
});
```

### `useTheme`
Access current theme in components.

```tsx
import { useTheme } from '@inkjs/ui';

function ThemedComponent() {
  const theme = useTheme();
  return <Text color={theme.colors.text}>Themed</Text>;
}
```

### Theme Customization Example

```tsx
const customTheme = extendTheme(defaultTheme, {
  components: {
    Spinner: {
      styles: {
        frame: () => ({ color: 'magenta' }),
      },
    },
    TextInput: {
      styles: {
        placeholder: () => ({ color: 'gray' }),
      },
    },
  },
});
```

## Reference Files

Comprehensive documentation in `references/`:
- **selection.md** - Selection components (Select, MultiSelect, ConfirmInput)
- **feedback.md** - Feedback components (Spinner, ProgressBar, StatusMessage, Alert, Badge)
- **other.md** - Other documentation and usage examples

Use `view` to read specific reference files when detailed information is needed.

## Working with This Skill

### For Beginners
Start with `other.md` for installation and basic setup, then explore individual component categories.

### For Text Inputs
Refer to `other.md` for TextInput, PasswordInput, EmailInput, ConfirmInput usage.

### For Selection Controls
Refer to `selection.md` for Select, MultiSelect, ConfirmInput.

### For Feedback Components
Refer to `feedback.md` for Spinner, ProgressBar, StatusMessage, Alert, Badge.

### For Theming
See `other.md` for ThemeProvider, extendTheme, and useTheme usage.

### For Code Examples
The quick reference section above contains common patterns extracted from official docs.

## Resources

### references/
Organized documentation extracted from official sources with detailed explanations and code examples.

### Official Docs
- **Ink-UI:** https://github.com/vadimdemedes/ink-ui

### scripts/
Add helper scripts for common automation tasks.

### assets/
Add templates, boilerplate, or example projects here.

## Notes

- This skill was generated from Ink-UI documentation
- Reference files preserve structure and examples from source docs
- Code examples include language detection for better syntax highlighting
- Quick reference patterns extracted from common usage

## Updating

To refresh this skill with updated documentation:
1. Re-run scraper with same configuration
2. The skill will be rebuilt with latest information
