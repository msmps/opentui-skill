# Testing OpenTUI Applications

How to test terminal user interfaces built with OpenTUI.

## Overview

OpenTUI provides:
- **Test Renderer**: Headless renderer for testing
- **Snapshot Testing**: Verify visual output
- **Interaction Testing**: Simulate user input

## When to Use

Use this reference when you need snapshot tests, interaction testing, or renderer-based regression checks.

## Test Setup

### Bun Test Runner

OpenTUI uses Bun's built-in test runner:

```typescript
import { test, expect, beforeEach, afterEach } from "bun:test"
```

### Test Renderer

Create a test renderer for headless testing:

```typescript
import { createTestRenderer } from "@opentui/core/testing"

const testSetup = await createTestRenderer({
  width: 80,     // Terminal width
  height: 24,    // Terminal height
})
```

## Core Testing

### Basic Test

```typescript
import { test, expect } from "bun:test"
import { createTestRenderer } from "@opentui/core/testing"
import { TextRenderable } from "@opentui/core"

test("renders text", async () => {
  const { renderer, snapshot } = await createTestRenderer({
    width: 40,
    height: 10,
  })
  
  const text = new TextRenderable(renderer, {
    id: "greeting",
    content: "Hello, World!",
  })
  
  renderer.root.add(text)
  
  expect(snapshot()).toContain("Hello, World!")
})
```

### Snapshot Testing

```typescript
test("component matches snapshot", async () => {
  const { renderer, snapshot } = await createTestRenderer({
    width: 40,
    height: 10,
  })
  
  // Create your component
  const box = new BoxRenderable(renderer, {
    id: "box",
    border: true,
    width: 20,
    height: 5,
  })
  box.add(new TextRenderable(renderer, {
    content: "Content",
  }))
  
  renderer.root.add(box)
  
  expect(snapshot()).toMatchSnapshot()
})
```

## React Testing

### Test Utilities

Create a helper for React tests:

```tsx
// test-utils.tsx
import { createTestRenderer } from "@opentui/core/testing"
import { createRoot } from "@opentui/react"

export async function renderForTest(
  element: React.ReactElement,
  options = { width: 80, height: 24 }
) {
  const testSetup = await createTestRenderer(options)
  createRoot(testSetup.renderer).render(element)
  
  // Allow React to render
  await new Promise(resolve => setTimeout(resolve, 0))
  
  return testSetup
}
```

### Component Test

```tsx
import { test, expect } from "bun:test"
import { renderForTest } from "./test-utils"

function Greeting({ name }: { name: string }) {
  return <text>Hello, {name}!</text>
}

test("Greeting renders name", async () => {
  const { snapshot } = await renderForTest(
    <Greeting name="World" />
  )
  
  expect(snapshot()).toContain("Hello, World!")
})
```

### State Testing

```tsx
import { test, expect } from "bun:test"
import { renderForTest } from "./test-utils"
import { useState } from "react"

function Counter() {
  const [count, setCount] = useState(0)
  return (
    <box>
      <text>Count: {count}</text>
    </box>
  )
}

test("Counter shows initial value", async () => {
  const { snapshot } = await renderForTest(<Counter />)
  
  expect(snapshot()).toContain("Count: 0")
})
```

## Solid Testing

### Test Utilities

```tsx
// test-utils.tsx
import { testRender } from "@opentui/solid"

export async function renderForTest(
  Component: () => JSX.Element,
  options = { width: 80, height: 24 }
) {
  return await testRender(Component, options)
}
```

### Component Test

```tsx
import { test, expect } from "bun:test"
import { renderForTest } from "./test-utils"

function Greeting(props: { name: string }) {
  return <text>Hello, {props.name}!</text>
}

test("Greeting renders name", async () => {
  const { snapshot } = await renderForTest(
    () => <Greeting name="World" />
  )
  
  expect(snapshot()).toContain("Hello, World!")
})
```

## Snapshot Format

Snapshots capture the rendered terminal output as text:

```
┌──────────────────┐
│ Hello, World!    │
│                  │
└──────────────────┘
```

### Updating Snapshots

```bash
bun test --update-snapshots
```

## Interaction Testing

### Simulating Key Presses

```typescript
test("responds to keyboard", async () => {
  const { renderer, snapshot } = await createTestRenderer({
    width: 40,
    height: 10,
  })
  
  // Create component that responds to keys
  // ...
  
  // Simulate keypress
  renderer.keyInput.emit("keypress", {
    name: "enter",
    sequence: "\r",
    ctrl: false,
    shift: false,
    meta: false,
    option: false,
    eventType: "press",
    repeated: false,
  })
  
  // Allow component to update
  await new Promise(resolve => setTimeout(resolve, 0))
  
  expect(snapshot()).toContain("Selected")
})
```

### Testing Focus

```typescript
test("input receives focus", async () => {
  const { renderer } = await createTestRenderer({
    width: 40,
    height: 10,
  })
  
  const input = new InputRenderable(renderer, {
    id: "test-input",
    placeholder: "Type here",
  })
  renderer.root.add(input)
  
  input.focus()
  
  expect(input.isFocused()).toBe(true)
})
```

## Test Organization

### File Structure

```
src/
├── components/
│   ├── Button.tsx
│   └── Button.test.tsx
├── hooks/
│   ├── useCounter.ts
│   └── useCounter.test.ts
└── test-utils.tsx
```

### Running Tests

```bash
# Run all tests
bun test

# Run specific test file
bun test src/components/Button.test.tsx

# Run with filter
bun test --filter "Button"

# Watch mode
bun test --watch
```

## Patterns

### Testing Conditional Rendering

```tsx
test("shows loading state", async () => {
  const { snapshot } = await renderForTest(
    <DataLoader loading={true} />
  )
  
  expect(snapshot()).toContain("Loading...")
})

test("shows data when loaded", async () => {
  const { snapshot } = await renderForTest(
    <DataLoader loading={false} data={["Item 1", "Item 2"]} />
  )
  
  expect(snapshot()).toContain("Item 1")
  expect(snapshot()).toContain("Item 2")
})
```

### Testing Lists

```tsx
test("renders all items", async () => {
  const items = ["Apple", "Banana", "Cherry"]
  
  const { snapshot } = await renderForTest(
    <ItemList items={items} />
  )
  
  items.forEach(item => {
    expect(snapshot()).toContain(item)
  })
})
```

### Testing Layouts

```tsx
test("matches layout snapshot", async () => {
  const { snapshot } = await renderForTest(
    <AppLayout />,
    { width: 120, height: 40 }  // Larger viewport
  )
  
  expect(snapshot()).toMatchSnapshot()
})
```

## Debugging Tests

### Print Snapshot

```typescript
test("debug output", async () => {
  const { snapshot } = await renderForTest(<MyComponent />)
  
  // Print to see what's rendered
  console.log(snapshot())
  
  expect(snapshot()).toContain("expected")
})
```

### Verbose Mode

```bash
bun test --verbose
```

## Gotchas

### Async Rendering

Wait for renders to complete:

```typescript
// Allow React/Solid to render
await new Promise(resolve => setTimeout(resolve, 0))
```

### Test Isolation

Each test should create its own renderer:

```typescript
test("test 1", async () => {
  const { renderer } = await createTestRenderer()
  // ...
})

test("test 2", async () => {
  const { renderer } = await createTestRenderer()
  // ...
})
```

### Snapshot Dimensions

Be consistent with test dimensions for stable snapshots:

```typescript
const { snapshot } = await createTestRenderer({
  width: 80,   // Standard width
  height: 24,  // Standard height
})
```

### Running from Package Directory

Run tests from the package directory:

```bash
cd packages/core
bun test

# Not from repo root for package-specific tests
```
