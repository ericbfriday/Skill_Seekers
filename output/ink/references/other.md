# Ink-Ui - Other

**Pages:** 10

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/email-input.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/status-message.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/unordered-list.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/ordered-list.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/progress-bar.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/password-input.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/readme.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/text-input.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui/blob/main/docs/confirm-input.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink-ui

**Contents:**
  - Uh oh!
  - License
  - Uh oh!
- vadimdemedes/ink-ui
- Folders and files
- Latest commit
- History
- Repository files navigation
- Ink UI
- Install

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

💄 Ink-redible command-line interfaces made easy

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

Collection of customizable UI components for CLIs made with Ink.

This assumes you've already set up Ink. The easiest way to get started is create-ink-app.

TextInput is used for entering any single-line input with an optional autocomplete.

EmailInput is used for entering an email. After "@" character is entered, domain can be autocompleted from the list of most popular email providers.

PasswordInput is used for entering sensitive data, like passwords, API keys and so on. It works the same way as TextInput, except input value is masked and replaced with asterisks ("*").

ConfirmInput shows a common "Y/n" input to confirm or cancel an operation your CLI wants to perform.

Select shows a scrollable list of options for a user to choose from.

MultiSelect is similar to Select, except user can choose multiple options.

Spinner indicates that something is being processed and CLI is waiting for it to complete.

ProgressBar is an extended version of Spinner, where it's possible to calculate a progress percentage.

Badge can be used to indicate a status of a certain item, usually positioned nearby the element it's related to.

StatusMessage can also be used to indicate a status, but when longer explanation of such status is required.

Alert is used to focus user's attention to important messages.

UnorderedList is used to show lists of items.

OrderedList is used to show lists of numbered items.

All component have their styles defined in a theme, which is accessible to components via React context. Ink UI ships with a default theme and it can be customized or replaced with a different theme altogether.

Let's get a quick look on how to customize a Spinner's component theme. Here's how it looks by default:

First, look up component's default theme, which will give an overview which parts does this component consist of. Documentation of each component includes a link to component's theme.ts file on top. In the case of Spinner, it's source/components/spinner/theme.ts.

Here's the part we care about:

This component theme hints that Spinner has 3 parts: container, frame and a label. So to customize the color of the spinner itself, we'd want to change the color prop returned from the frame function.

To customize the default theme, use extendTheme function and make that custom theme available to children components via ThemeProvider.

With custom theme applied, Spinner now renders a magenta spinner, instead of the default blue one.

There are also cases where styles change based on some condition. For example, StatusMessage changes the color of an icon based on the variant prop.

Here's a sample code from its theme.

Since each field in styles object is a function, it can return different styles based on the props that were passed in or a state of a component.

Component themes can also include configuration for rendering a component in a config object, that's not related to styling. For example, UnorderedList specifies a marker, which is a character that's rendered before each list item.

Here's a sample code from its theme.

Changing marker to '+' would render this:

Components shipped in Ink UI automatically read the necessary styles and configuration from a theme. However, if you're adding a new custom component and a theme for it, use useComponentTheme hook to access it.

💄 Ink-redible command-line interfaces made easy

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

**Examples:**

Example 1 (python):
```python
npm install @inkjs/ui
```

Example 2 (jsx):
```jsx
import {TextInput} from '@inkjs/ui';

<TextInput
	placeholder="Enter your name..."
	onSubmit={name => {
		// `name` contains user input
	}}
/>;
```

Example 3 (jsx):
```jsx
import {EmailInput} from '@inkjs/ui';

<EmailInput
	placeholder="Enter email..."
	onSubmit={email => {
		// `email` contains user input
	}}
/>;
```

Example 4 (jsx):
```jsx
import {PasswordInput} from '@inkjs/ui';

<PasswordInput
	placeholder="Enter password..."
	onSubmit={password => {
		// `password` contains user input
	}}
/>;
```

---
