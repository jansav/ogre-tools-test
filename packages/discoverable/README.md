# @lensapp/discoverable

A test utility to locate and interact with HTML elements using `data-*-test` attributes.

## Usage

```tsx
import { render } from '@testing-library/react';
import { discoverFor } from '@lensapp/discoverable';

const rendered = render(
  <div>
    <div
      data-some-attribute-test="some-value"
      data-some-other-attribute-test="some-other-value"
    />
    <div data-some-unrelated-attribute-test="irrelevant" />
  </div>,
);

const discover = discoverFor(() => rendered);

const discovered = discover.getSingleElement('some-attribute', 'some-value');

console.log(discovered.getAttribute('some-other-attribute'));
// -> "some-other-value"

await discovered.click();
```

## Exports

### `discoverFor(getSource)`

Creates a `Discover` object bound to a DOM source (a `RenderResult`, an `Element`, or an `Element[]`). The source is re-evaluated lazily on each call, so the render result does not need to be stable.

```ts
const discover = discoverFor(() => rendered);
discover.getSingleElement('my-button').click();
```

### `getSingleElement(getSource)`

Returns a `GetSingleElement` function bound to the given source — identical to `discoverFor(getSource).getSingleElement` but usable standalone.

```ts
const getButton = getSingleElement(() => rendered);
getButton('submit-button').click();
```

### `queryAllElements(getSource)`

Returns a `QueryAllElements` function bound to the given source — identical to `discoverFor(getSource).queryAllElements` but usable standalone.

```ts
const getItems = queryAllElements(() => rendered);
const { discovered } = getItems('list-item');
```

### `querySingleElement(getSource)`

Returns a `QuerySingleElement` function bound to the given source — identical to `discoverFor(getSource).querySingleElement` but usable standalone. Unlike `getSingleElement`, this does not throw when the element is absent; `discovered` is `null` instead.

```ts
const maybeButton = querySingleElement(() => rendered);
const { discovered } = maybeButton('optional-button');
if (discovered) { /* ... */ }
```

## Types

### `Discover`

Interface exposing `getSingleElement`, `queryAllElements`, and `querySingleElement` methods, all bound to the same source.

### `Discovered`

Object returned by `getSingleElement`. Contains:
- `discovered: Element` — the matched DOM element (guaranteed non-null)
- `attributeValue: string | null` — value of the matched `data-*-test` attribute
- `getAttribute(name)` — reads `data-{name}-test` from the element
- All `Actionable` interaction methods
- All `Discover` methods for chained nested discovery

### `MaybeDiscovered`

Object returned by `querySingleElement`. Like `Discovered` but `discovered` may be `null` and `attributeValue` / `getAttribute` are absent.

### `Actionable`

Methods available on `Discovered` and `MaybeDiscovered` for user-event interactions:
- `click()`, `doubleClick()`, `rightClick()`
- `hover()`, `unhover()`
- `type(data)` — types the given string into the element

### `GetSingleElement`

Function type `(attributeName: string, attributeValue?: string) => Discovered`.

### `QueryAllElements`

Function type `(attributeName: string, attributeValue?: string) => QueryManyDiscovered`.

### `QuerySingleElement`

Function type `(attributeName: string, attributeValue?: string) => MaybeDiscovered`.
