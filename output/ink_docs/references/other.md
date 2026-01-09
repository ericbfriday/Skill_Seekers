# Ink_Docs - Other

**Pages:** 2

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink/blob/master/readme.md

**Contents:**
  - Uh oh!
  - Uh oh!

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

---

## Navigation Menu

**URL:** https://github.com/vadimdemedes/ink

**Contents:**
  - Uh oh!
  - License
  - Uh oh!
- vadimdemedes/ink
- Folders and files
- Latest commit
- History
- Repository files navigation
- Install
- Usage

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

🌈 React for interactive command-line apps

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

React for CLIs. Build and test your CLI output using components.

Ink provides the same component-based UI building experience that React offers in the browser, but for command-line apps. It uses Yoga to build Flexbox layouts in the terminal, so most CSS-like properties are available in Ink as well. If you are already familiar with React, you already know Ink.

Since Ink is a React renderer, all features of React are supported. Head over to the React website for documentation on how to use it. Only Ink's methods are documented in this readme.

My open source work is supported by the community ❤️

Feel free to play around with the code and fork this Repl at https://repl.it/@vadimdemedes/ink-counter-demo.

(PRs welcome. Append new entries at the end. Repos must have 100+ stars and showcase Ink beyond a basic list picker.)

Use create-ink-app to quickly scaffold a new Ink-based CLI.

Alternatively, create a TypeScript project:

Ink requires the same Babel setup as you would do for regular React-based apps in the browser.

Set up Babel with a React preset to ensure all examples in this readme work as expected. After installing Babel, install @babel/preset-react and insert the following configuration in babel.config.json:

Next, create a file source.js, where you'll type code that uses Ink:

Then, transpile this file with Babel:

Now you can run cli.js with Node.js:

If you don't like transpiling files during development, you can use import-jsx or @esbuild-kit/esm-loader to import a JSX file and transpile it on the fly.

Ink uses Yoga, a Flexbox layout engine, to build great user interfaces for your CLIs using familiar CSS-like properties you've used when building apps for the browser. It's important to remember that each element is a Flexbox container. Think of it as if every <div> in the browser had display: flex. See <Box> built-in component below for documentation on how to use Flexbox layouts in Ink. Note that all text must be wrapped in a <Text> component.

This component can display text and change its style to make it bold, underlined, italic, or strikethrough.

Note: <Text> allows only text nodes and nested <Text> components inside of it. For example, <Box> component can't be used inside <Text>.

Change text color. Ink uses chalk under the hood, so all its functionality is supported.

Same as color above, but for background.

Type: boolean Default: false

Dim the color (make it less bright).

Type: boolean Default: false

Type: boolean Default: false

Make the text italic.

Type: boolean Default: false

Make the text underlined.

Type: boolean Default: false

Make the text crossed with a line.

Type: boolean Default: false

Invert background and foreground colors.

Type: string Allowed values: wrap truncate truncate-start truncate-middle truncate-end Default: wrap

This property tells Ink to wrap or truncate text if its width is larger than the container. If wrap is passed (the default), Ink will wrap text and split it into multiple lines. If truncate-* is passed, Ink will truncate text instead, resulting in one line of text with the rest cut off.

<Box> is an essential Ink component to build your layout. It's like <div style="display: flex"> in the browser.

Width of the element in spaces. You can also set it as a percentage, which will calculate the width based on the width of the parent element.

Height of the element in lines (rows). You can also set it as a percentage, which will calculate the height based on the height of the parent element.

Sets a minimum width of the element. Percentages aren't supported yet; see facebook/yoga#872.

Sets a minimum height of the element. Percentages aren't supported yet; see facebook/yoga#872.

Type: number Default: 0

Type: number Default: 0

Type: number Default: 0

Type: number Default: 0

Type: number Default: 0

Horizontal padding. Equivalent to setting paddingLeft and paddingRight.

Type: number Default: 0

Vertical padding. Equivalent to setting paddingTop and paddingBottom.

Type: number Default: 0

Padding on all sides. Equivalent to setting paddingTop, paddingBottom, paddingLeft and paddingRight.

Type: number Default: 0

Type: number Default: 0

Type: number Default: 0

Type: number Default: 0

Type: number Default: 0

Horizontal margin. Equivalent to setting marginLeft and marginRight.

Type: number Default: 0

Vertical margin. Equivalent to setting marginTop and marginBottom.

Type: number Default: 0

Margin on all sides. Equivalent to setting marginTop, marginBottom, marginLeft and marginRight.

Type: number Default: 0

Size of the gap between an element's columns and rows. A shorthand for columnGap and rowGap.

Type: number Default: 0

Size of the gap between an element's columns.

Type: number Default: 0

Size of the gap between an element's rows.

Type: number Default: 0

Type: number Default: 1

Type: string Allowed values: row row-reverse column column-reverse

Type: string Allowed values: nowrap wrap wrap-reverse

Type: string Allowed values: flex-start center flex-end

Type: string Default: auto Allowed values: auto flex-start center flex-end

Type: string Allowed values: flex-start center flex-end space-between space-around space-evenly

Type: string Allowed values: flex none Default: flex

Set this property to none to hide the element.

Type: string Allowed values: visible hidden Default: visible

Behavior for an element's overflow in the horizontal direction.

Type: string Allowed values: visible hidden Default: visible

Behavior for an element's overflow in the vertical direction.

Type: string Allowed values: visible hidden Default: visible

A shortcut for setting overflowX and overflowY at the same time.

Type: string Allowed values: single double round bold singleDouble doubleSingle classic | BoxStyle

Add a border with a specified style. If borderStyle is undefined (the default), no border will be added. Ink uses border styles from the cli-boxes module.

Alternatively, pass a custom border style like so:

See example in examples/borders.

Change border color. A shorthand for setting borderTopColor, borderRightColor, borderBottomColor, and borderLeftColor.

Change top border color. Accepts the same values as color in <Text> component.

Change the right border color. Accepts the same values as color in <Text> component.

Change the bottom border color. Accepts the same values as color in <Text> component.

Change the left border color. Accepts the same values as color in <Text> component.

Type: boolean Default: false

Dim the border color. A shorthand for setting borderTopDimColor, borderBottomDimColor, borderLeftDimColor, and borderRightDimColor.

Type: boolean Default: false

Dim the top border color.

Type: boolean Default: false

Dim the bottom border color.

Type: boolean Default: false

Dim the left border color.

Type: boolean Default: false

Dim the right border color.

Type: boolean Default: true

Determines whether the top border is visible.

Type: boolean Default: true

Determines whether the right border is visible.

Type: boolean Default: true

Determines whether the bottom border is visible.

Type: boolean Default: true

Determines whether the left border is visible.

Background color for the element.

Accepts the same values as color in the <Text> component.

The background color fills the entire <Box> area and is inherited by child <Text> components unless they specify their own backgroundColor.

Background colors work with borders and padding:

See example in examples/box-backgrounds.

Adds one or more newline (\n) characters. Must be used within <Text> components.

Type: number Default: 1

Number of newlines to insert.

A flexible space that expands along the major axis of its containing layout. It's useful as a shortcut for filling all the available space between elements.

For example, using <Spacer> in a <Box> with default flex direction (row) will position "Left" on the left side and will push "Right" to the right side.

In a vertical flex direction (column), it will position "Top" at the top of the container and push "Bottom" to the bottom. Note that the container needs to be tall enough to see this in effect.

<Static> component permanently renders its output above everything else. It's useful for displaying activity like completed tasks or logs - things that don't change after they're rendered (hence the name "Static").

It's preferred to use <Static> for use cases like these when you can't know or control the number of items that need to be rendered.

For example, Tap uses <Static> to display a list of completed tests. Gatsby uses it to display a list of generated pages while still displaying a live progress bar.

Note: <Static> only renders new items in the items prop and ignores items that were previously rendered. This means that when you add new items to the items array, changes you make to previous items will not trigger a rerender.

See examples/static for an example usage of <Static> component.

Array of items of any type to render using the function you pass as a component child.

Styles to apply to a container of child elements. See <Box> for supported properties.

Function that is called to render every item in the items array. The first argument is the item itself, and the second argument is the index of that item in the items array.

Note that a key must be assigned to the root component.

Transform a string representation of React components before they're written to output. For example, you might want to apply a gradient to text, add a clickable link, or create some text effects. These use cases can't accept React nodes as input; they expect a string. That's what the <Transform> component does: it gives you an output string of its child components and lets you transform it in any way.

Note: <Transform> must be applied only to <Text> children components and shouldn't change the dimensions of the output; otherwise, the layout will be incorrect.

Since the transform function converts all characters to uppercase, the final output rendered to the terminal will be "HELLO WORLD", not "Hello World".

When the output wraps to multiple lines, it can be helpful to know which line is being processed.

For example, to implement a hanging indent component, you can indent all the lines except for the first.

Function that transforms children output. It accepts children and must return transformed children as well.

Output of child components.

The zero-indexed line number of the line that's currently being transformed.

This hook is used for handling user input. It's a more convenient alternative to using useStdin and listening for data events. The callback you pass to useInput is called for each character when the user enters any input. However, if the user pastes text and it's more than one character, the callback will be called only once, and the whole string will be passed as input. You can find a full example of using useInput at examples/use-input.

The handler function that you pass to useInput receives two arguments:

The input that the program received.

Handy information about a key that was pressed.

Type: boolean Default: false

If an arrow key was pressed, the corresponding property will be true. For example, if the user presses the left arrow key, key.leftArrow equals true.

Type: boolean Default: false

Return (Enter) key was pressed.

Type: boolean Default: false

Escape key was pressed.

Type: boolean Default: false

Ctrl key was pressed.

Type: boolean Default: false

Shift key was pressed.

Type: boolean Default: false

Type: boolean Default: false

Backspace key was pressed.

Type: boolean Default: false

Delete key was pressed.

Type: boolean Default: false

If the Page Up or Page Down key was pressed, the corresponding property will be true. For example, if the user presses Page Down, key.pageDown equals true.

Type: boolean Default: false

Meta key was pressed.

Type: boolean Default: true

Enable or disable capturing of user input. Useful when there are multiple useInput hooks used at once to avoid handling the same input several times.

useApp is a React hook that exposes a method to manually exit the app (unmount).

Exit (unmount) the whole Ink app.

Optional error. If passed, waitUntilExit will reject with that error.

useStdin is a React hook that exposes the stdin stream.

Type: stream.Readable Default: process.stdin

The stdin stream passed to render() in options.stdin, or process.stdin by default. Useful if your app needs to handle user input.

A boolean flag determining if the current stdin supports setRawMode. A component using setRawMode might want to use isRawModeSupported to nicely fall back in environments where raw mode is not supported.

See setRawMode. Ink exposes this function to be able to handle Ctrl+C, that's why you should use Ink's setRawMode instead of process.stdin.setRawMode.

Warning: This function will throw unless the current stdin supports setRawMode. Use isRawModeSupported to detect setRawMode support.

useStdout is a React hook that exposes the stdout stream where Ink renders your app.

Type: stream.Writable Default: process.stdout

Write any string to stdout while preserving Ink's output. It's useful when you want to display external information outside of Ink's rendering and ensure there's no conflict between the two. It's similar to <Static>, except it can't accept components; it only works with strings.

Data to write to stdout.

See additional usage example in examples/use-stdout.

useStderr is a React hook that exposes the stderr stream.

Type: stream.Writable Default: process.stderr

Write any string to stderr while preserving Ink's output.

It's useful when you want to display external information outside of Ink's rendering and ensure there's no conflict between the two. It's similar to <Static>, except it can't accept components; it only works with strings.

Data to write to stderr.

A component that uses the useFocus hook becomes "focusable" to Ink, so when the user presses Tab, Ink will switch focus to this component. If there are multiple components that execute the useFocus hook, focus will be given to them in the order in which these components are rendered. This hook returns an object with an isFocused boolean property, which determines whether this component is focused.

Type: boolean Default: false

Auto-focus this component if there's no active (focused) component right now.

Type: boolean Default: true

Enable or disable this component's focus, while still maintaining its position in the list of focusable components. This is useful for inputs that are temporarily disabled.

Type: string Required: false

Set a component's focus ID, which can be used to programmatically focus the component. This is useful for large interfaces with many focusable elements to avoid having to cycle through all of them.

See example in examples/use-focus and examples/use-focus-with-id.

This hook exposes methods to enable or disable focus management for all components or manually switch focus to the next or previous components.

Enable focus management for all components.

Note: You don't need to call this method manually unless you've disabled focus management. Focus management is enabled by default.

Disable focus management for all components. The currently active component (if there's one) will lose its focus.

Switch focus to the next focusable component. If there's no active component right now, focus will be given to the first focusable component. If the active component is the last in the list of focusable components, focus will be switched to the first focusable component.

Note: Ink calls this method when user presses Tab.

Switch focus to the previous focusable component. If there's no active component right now, focus will be given to the first focusable component. If the active component is the first in the list of focusable components, focus will be switched to the last focusable component.

Note: Ink calls this method when user presses Shift+Tab.

Switch focus to the component with the given id. If there's no component with that ID, focus will be given to the next focusable component.

Returns whether a screen reader is enabled. This is useful when you want to render different output for screen readers.

Mount a component and render the output.

Type: stream.Writable Default: process.stdout

Output stream where the app will be rendered.

Type: stream.Readable Default: process.stdin

Input stream where app will listen for input.

Type: stream.Writable Default: process.stderr

Type: boolean Default: true

Configure whether Ink should listen for Ctrl+C keyboard input and exit the app. This is needed in case process.stdin is in raw mode, because then Ctrl+C is ignored by default and the process is expected to handle it manually.

Type: boolean Default: true

Patch console methods to ensure console output doesn't mix with Ink's output. When any of the console.* methods are called (like console.log()), Ink intercepts their output, clears the main output, renders output from the console method, and then rerenders the main output again. That way, both are visible and don't overlap each other.

This functionality is powered by patch-console, so if you need to disable Ink's interception of output but want to build something custom, you can use that.

Type: ({renderTime: number}) => void Default: undefined

Runs the given callback after each render and re-render with a metrics object.

Type: boolean Default: false

If true, each update will be rendered as separate output, without replacing the previous one.

Type: number Default: 30

Maximum frames per second for render updates. This controls how frequently the UI can update to prevent excessive re-rendering. Higher values allow more frequent updates but may impact performance. Setting it to a lower value may be useful for components that update very frequently, to reduce CPU usage.

Type: boolean Default: false

Enable incremental rendering mode which only updates changed lines instead of redrawing the entire output. This can reduce flickering and improve performance for frequently updating UIs.

This is the object that render() returns.

Replace the previous root node with a new one or update the props of the current root node.

Manually unmount the whole Ink app.

Returns a promise that resolves when the app is unmounted.

Measure the dimensions of a particular <Box> element. Returns an object with width and height properties. This function is useful when your component needs to know the amount of available space it has. You can use it when you need to change the layout based on the length of its content.

Note: measureElement() returns correct results only after the initial render, when the layout has been calculated. Until then, width and height equal zero. It's recommended to call measureElement() in a useEffect hook, which fires after the component has rendered.

A reference to a <Box> element captured with the ref property. See Refs for more information on how to capture references.

Ink components are simple to test with ink-testing-library. Here's a simple example that checks how the component is rendered:

Check out ink-testing-library for more examples and full documentation.

Ink supports React Devtools out of the box. To enable integration with React Devtools in your Ink-based CLI, first ensure you have installed the optional react-devtools-core dependency, and then run your app with the DEV=true environment variable:

Then, start React Devtools itself:

After it starts, you should see the component tree of your CLI. You can even inspect and change the props of components, and see the results immediately in the CLI, without restarting it.

Note: You must manually quit your CLI via Ctrl+C after you're done testing.

Ink has basic support for screen readers.

To enable it, you can either pass the isScreenReaderEnabled option to the render function or set the INK_SCREEN_READER environment variable to true.

Ink implements a small subset of functionality from the ARIA specification.

When screen reader support is enabled, Ink will try its best to generate a screen-reader-friendly output.

For example, for this code:

Ink will generate the following output for screen readers:

You can also provide a custom label for screen readers if you want to render something different for them.

For example, if you are building a progress bar, you can use aria-label to provide a more descriptive label for screen readers.

In the example above, the screen reader will read "Progress: 50%" instead of "50%".

A label for the element for screen readers.

Type: boolean Default: false

Hide the element from screen readers.

The role of the element.

The state of the element.

When building custom components, it's important to keep accessibility in mind. While Ink provides the building blocks, ensuring your components are accessible will make your CLIs usable by a wider audience.

For a practical example of building an accessible component, see the ARIA example.

The examples directory contains a set of real examples. You can run them with:

🌈 React for interactive command-line apps

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

There was an error while loading. Please reload this page.

**Examples:**

Example 1 (unknown):
```unknown
npm install ink react
```

Example 2 (jsx):
```jsx
import React, {useState, useEffect} from 'react';
import {render, Text} from 'ink';

const Counter = () => {
	const [counter, setCounter] = useState(0);

	useEffect(() => {
		const timer = setInterval(() => {
			setCounter(previousCounter => previousCounter + 1);
		}, 100);

		return () => {
			clearInterval(timer);
		};
	}, []);

	return <Text color="green">{counter} tests passed</Text>;
};

render(<Counter />);
```

Example 3 (unknown):
```unknown
npx create-ink-app my-ink-cli
```

Example 4 (unknown):
```unknown
npx create-ink-app --typescript my-ink-cli
```

---
