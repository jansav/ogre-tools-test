# `@lensapp/injectable-react`

React bindings for `@lensapp/injectable` — hooks and HOCs for injecting dependencies into React components.

## Installation

```
npm install @lensapp/injectable
npm install @lensapp/injectable-react
```

## Usage

```tsx
import { createContainer } from '@lensapp/injectable';
import {
  DiContextProvider,
  withInjectables,
  getInjectableComponent,
  useInject,
} from '@lensapp/injectable-react';

const di = createContainer('my-container');

// Provide the DI container to your React tree
<DiContextProvider value={di}>
  <App />
</DiContextProvider>
```

### `withInjectables` — HOC for injecting into regular components

```tsx
const NonInjectedComponent = ({ someDependency, ...props }) => (
  <div {...props}>Some content: "{someDependency}"</div>
);

const InjectedComponent = withInjectables(NonInjectedComponent, {
  getProps: (di, props) => ({
    someDependency: di.inject(someDependencyInjectable),
    ...props,
  }),
});
```

For async dependencies, provide a `getPlaceholder` alongside an async `getProps`:

```tsx
const InjectedComponent = withInjectables(NonInjectedComponent, {
  getProps: async (di, props) => ({
    someDependency: await di.inject(someAsyncInjectable),
    ...props,
  }),
  getPlaceholder: props => <Spinner />,
});
```

### `getInjectableComponent` — self-registering injectable React component

Creates a React component that is also an injectable, so it can be registered, overridden, and injected via the DI container.

```tsx
const MyComponent = getInjectableComponent({
  id: 'my-component',
  Component: ({ name }) => <div>Hello, {name}!</div>,
});

di.register(MyComponent);

// Use it like a normal React component:
<MyComponent name="World" />
```

Optionally provide a `PlaceholderComponent` for Suspense support and an `injectionToken` to inject it via a shared token:

```tsx
const MyComponent = getInjectableComponent({
  id: 'my-component',
  Component: ({ name }) => <div>Hello, {name}!</div>,
  PlaceholderComponent: () => <Spinner />,
  injectionToken: myComponentToken,
});
```

### `useInject` — inject inside a component (suspends on async)

Injects an injectable or injection token inside a functional component. If the injected value is a promise, the component suspends until it resolves.

```tsx
const MyComponent = () => {
  const someThing = useInject(someInjectable);
  return <div>{someThing}</div>;
};
```

Accepts an optional instantiation parameter:

```tsx
const value = useInject(someKeyedInjectable, 'my-key');
```

### `useInjectDeferred` — inject with deferred updates (keeps stale value while loading)

Same as `useInject`, but uses React's `useDeferredValue` — the component keeps showing the previous value while a new async instance is resolving, instead of suspending.

```tsx
const MyComponent = () => {
  const someThing = useInjectDeferred(someAsyncInjectable);
  return <div>{someThing}</div>;
};
```

## API

### `DiContextProvider`

`React.Provider<DiContainer>` — provides the DI container to the component tree. All hooks and `getInjectableComponent`-based components must be rendered inside a `DiContextProvider`.

### `withInjectables(Component, options)`

Higher-order component that resolves dependencies from the DI context and passes them as props to the wrapped component.

| Option | Description |
|---|---|
| `getProps(di, props)` | Synchronous or async function that returns the merged props including injected dependencies |
| `getPlaceholder?(props)` | Rendered while async `getProps` is resolving |

### `getInjectableComponent(options)`

Creates a React component that is simultaneously an injectable. The returned component can be registered with `di.register()`, overridden with `di.override()`, and used directly as a JSX element.

| Option | Description |
|---|---|
| `id` | Unique string identifier for the injectable |
| `Component` | The underlying React component to render |
| `PlaceholderComponent?` | Shown via `Suspense` while the component suspends |
| `injectionToken?` | Injection token the component is registered under |
| `causesSideEffects?` | Mark as a side-effect-causing injectable |
| `tags?` | Array of tags for auto-registration filtering |

### `useInject(injectable, parameter?)`

React hook that injects a value from the DI container. Suspends if the injected value is a promise. The component will re-suspend whenever the parameter changes and the new instance is not yet resolved.

### `useInjectDeferred(injectable, parameter?)`

Same as `useInject` but wraps the injected value with `useDeferredValue` — the previously resolved value is shown while a new async instance is loading, preventing unnecessary Suspense fallbacks on parameter changes.
