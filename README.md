# 🛰️ XJS - v0.0.1

## Ultra-lightweight UI library - Minimal & Zero Dependencies ~0.8kb (gzipped)

A high-performance, ~0.8kb (gzipped) reactive UI library. It provides fine-grained reactivity and a declarative DOM-building experience using native JavaScript Proxies—without a Virtual DOM, build step, or dependencies.

# ✨ Key Features

Fine-Grained Reactivity: Only the specific DOM nodes linked to a signal update. No tree diffing.

Automatic Garbage Collection: Signals automatically unsubscribe from DOM nodes when they are removed from the document.

Native Proxy Builder: Use h.div() instead of strings or JSX.

Memory Safe: Uses isConnected checks to prevent detached DOM node leaks.

Zero Dependencies: 100% vanilla JavaScript.

# 🚀 Installation

Save the library files and import them into your project:

```JavaScript

import { html, signal, append } from 'https://cdn.jsdelivr.net/gh/xjs-org/xjs@main/release/html.min.js';
```
```JavaScript

import { apiProxy } from 'https://cdn.jsdelivr.net/gh/xjs-org/xjs@main/release/api.min.js';
```


## CDN link

``` html
<script src="https://cdn.jsdelivr.net/gh/xjs-org/xjs@main/release/xjs.min.js" crossorigin></script>
```
```JavaScript

const { div } = XJS.htmlElements;
const count = XJS.createSignal(0);
const methods = XJS.apiProxy("api-endpoint-url", {options})
```

# 📖 API Reference

### signal(initialValue)

Creates a reactive state container.

### 1 .value:

Get or set the current state.

### 2 .isSignal:

Identify if its instance of signal.

### 3 onSignal(callback):

Manually subscribe to changes.

### html(namespace?)

A Proxy factory for creating DOM elements.

h.tagName(...args): Creates a standard HTML element.

h.Fragment(...args): Group elements without a wrapper div.

append(element, ...children)
A utility to safely append children to a node. Handles strings, Nodes, nested arrays, and reactive Signals.

# 🛠 Usage Examples

## 1. Basic Counter

The bread and butter of reactivity. Notice how count is passed directly as a child.

```javascript
const h = html();
const count = signal(0);

const counter = h.div(
  h.h1("Counter"),
  h.p("Current value: ", count),
  h.button("Increment").on("click", () => count.value++)
);

document.body.append(counter);
```

2. Functional Derivations (Computed)

If you pass a function as a child or attribute, the library automatically tracks any signals used inside it.

```javascript
const name = signal("world");

const greeting = h.div(
  h.input({
    placeholder: "Type name...",
    oninput: (e) => (name.value = e.target.value),
  }),
  h.p(() => `Hello, ${name.value.toUpperCase()}!`)
);
```

## 3. Dynamic Attributes

You can bind signals or functions to any HTML attribute.

```JavaScript

const isEnabled = signal(false);

const submitBtn = h.button({
class: () => isEnabled.value ? "btn-active" : "btn-disabled",
disabled: () => !isEnabled.value
}, "Submit");
```

# 🏗 Technical Architecture

## How Reactivity Works

The library uses a global Dependency Tracker.

When a component is built, it enters a bind state.

Any signal.value accessed during this state adds the current DOM element to its internal listener list.

When the signal value changes, it triggers the specific setter (e.g., setAttribute or replaceChild) associated with that element.

## Memory Management & GC

Unlike other libraries that require manual cleanup() or useEffect returns, this library utilizes the browser's Node.isConnected API.

Every time a signal updates, it filters its internal listener list.

If an element is no longer in the DOM, the signal forgets it.

This makes it ideal for Single Page Applications (SPAs) where components are frequently created and destroyed.

# ⚠️ Best Practices & Constraints

## Boolean Attributes:

For attributes like disabled or checked, return "" to enable and null to remove the attribute.

```JavaScript

h.input({ disabled: () => isActive.value ? null : "" })

```
## Wrap if not last
``` javascript
const count = signal(0);
const { div, span } = html();

// BREAKS: Signal is isolated in its own parent span
div("Total ", () => count.value, " values");

// WORKS: Signal is the last child
div("Total values: ", () => count.value);

// WORKS: Signal is isolated in its own parent span
div("Total ",
  span(() => count.value),
" values");

```

## MFE Usage:

This library is safe for Micro Front-ends because it uses Object.defineProperty to attach .on() helpers, ensuring no pollution of the global HTMLElement prototype.

## List Rendering:

For large lists, it is currently more efficient to handle array updates imperatively or by wrapping the list in a single functional derivation.

# 📄 License

MIT
