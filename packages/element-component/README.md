# @lensapp/element-component

A way to introduce re-usable props in React.

## Usage

```tsx
import { getElementComponent, getPlugin } from '@lensapp/element-component';

const somePlugin = getPlugin((props: { $somePluginProp: string }) => ({
  title: props.$somePluginProp,
  $somePluginProp: undefined,
}));

const SomeElementComponent = getElementComponent('div', somePlugin);

render(<SomeElementComponent $somePluginProp="some-value" />);

// -> <div title="some-value" />
```

## Exports

### `getElementComponent(tagName, ...plugins)`

Creates a React component for the given HTML tag name with optional plugins applied. The resulting component merges plugin-provided props with the element's native HTML props.

```tsx
const Button = getElementComponent('button');
// -> React component for <button> with no extra props

const StyledButton = getElementComponent('button', colorPlugin, sizePlugin);
// -> React component accepting plugin props in addition to native button props
```

### `getPlugin(plugin)`

Wraps a prop-transformation function as a typed plugin. Plugins receive custom props (prefixed with `$`) and return native HTML props with the custom props set to `undefined`.

```tsx
const disabledPlugin = getPlugin((props: { $isDisabled: boolean }) => ({
  disabled: props.$isDisabled,
  $isDisabled: undefined,
}));

const DisabledButton = getElementComponent('button', disabledPlugin);
render(<DisabledButton $isDisabled={true} />);
// -> <button disabled />
```

### `ElementComponent<TTagName, TProps>`

Type for a React component created by `getElementComponent`. Combines plugin-provided props with the intrinsic HTML props of the tag.

### `Plugin<TInputProps, TOutputProps>`

Type for a plugin function: `(props: TInputProps) => TOutputProps`.

### `TagNames`

Type alias for all valid JSX intrinsic element tag names (e.g. `'div'`, `'button'`, `'span'`).
