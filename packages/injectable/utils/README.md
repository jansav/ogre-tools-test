# `@lensapp/injectable-utils`

Utilities for orchestrating ordered async/sync side-effect runners with `@lensapp/injectable`.

## Installation

```
npm install @lensapp/injectable
npm install @lensapp/injectable-utils
```

## Usage

```ts
import {
  createContainer,
  getInjectable,
  getInjectionToken,
} from '@lensapp/injectable';
import { runManyFor, type Runnable } from '@lensapp/injectable-utils';

const di = createContainer('my-container');

const startupToken = getInjectionToken<Runnable>({ id: 'startup' });

const someStartupStep = getInjectable({
  id: 'some-startup-step',
  instantiate: () => ({
    run: async () => {
      // do something async
    },
  }),
  injectionToken: startupToken,
});

di.register(someStartupStep);

// runManyFor returns a function that runs all registered runnables in order.
await runManyFor(di)(startupToken)();
```

### Ordered execution with `runAfter`

Runnables can declare that they must run after another runnable by referencing it via `runAfter`. Steps at the same level run concurrently; steps in later levels wait for the previous level to resolve.

```ts
const stepA = getInjectable({
  id: 'step-a',
  instantiate: () => ({ run: async () => { /* first */ } }),
  injectionToken: startupToken,
});

const stepB = getInjectable({
  id: 'step-b',
  instantiate: di => ({
    run: async () => { /* runs after step-a */ },
    runAfter: di.inject(stepA),
  }),
  injectionToken: startupToken,
});
```

## API

### `runManyFor(di)`

Returns a curried async runner for a given injection token.

```ts
runManyFor(di: DiContainerForInjection)(token)(parameter?)
```

| Parameter | Description |
|---|---|
| `di` | DI container to inject runnables from |
| `token` | Injection token whose registered `Runnable` implementations to run |
| `parameter` | Optional parameter forwarded to every `run` call |

Runs all runnables in dependency order (respecting `runAfter` chains). Runnables at the same level are executed concurrently. Returns a `Promise<void>` that resolves once all runnables have resolved.

### `runManySyncFor(di)`

Synchronous counterpart to `runManyFor`. Same signature and ordering semantics, but `run` functions are expected to return `void` instead of `Promise<void>`.

```ts
runManySyncFor(di: DiContainerForInjection)(token)(parameter?)
```

### `Runnable<TParameter>` interface

Implement this interface to create a runnable that can be registered under an injection token and executed with `runManyFor`.

| Property | Type | Description |
|---|---|---|
| `run` | `(parameter: TParameter) => Promise<void> \| void` | The async function to execute |
| `runAfter?` | `this` | Another runnable instance that must complete before this one starts |

### `RunnableSync<TParameter>` interface

Synchronous variant of `Runnable`. Used with `runManySyncFor`.

| Property | Type | Description |
|---|---|---|
| `run` | `(parameter: TParameter) => void` | The synchronous function to execute |
| `runAfter?` | `this` | Another runnable instance that must complete before this one starts |
