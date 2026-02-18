# @lensapp/test-utils

A collection of test utilities for Ogre-tools.

## Usage

```
$ npm install @lensapp/test-utils
```

```ts
import {
  getPromiseStatus,
  flushPromises,
  advanceFakeTime,
  advanceFakeTimeSlow,
} from '@lensapp/test-utils';
```

## Exports

### `getPromiseStatus(promise)`

Returns a promise that resolves to `{ fulfilled: boolean }` reflecting the current settlement state of the given promise. Useful for asserting whether a promise has resolved without awaiting it.

```ts
const promise = someAsyncOperation();
const { fulfilled } = await getPromiseStatus(promise);
expect(fulfilled).toBe(false);
```

### `flushPromises()`

Returns a promise that resolves after all currently pending micro-tasks (promise callbacks) have run. Useful for driving async code in tests without real timers.

```ts
await flushPromises();
```

### `advanceFakeTime(milliseconds)`

Advances Jest fake timers by `milliseconds` and flushes all resulting promises. Use this when the code under test relies on `setTimeout`/`setInterval` with fake timers.

```ts
jest.useFakeTimers();
await advanceFakeTime(1000); // advance 1 second and flush
```

### `advanceFakeTimeSlow(milliseconds)`

Like `advanceFakeTime`, but advances time in small increments, flushing promises between each increment. Useful for code that chains multiple timer-dependent async operations.

```ts
jest.useFakeTimers();
await advanceFakeTimeSlow(5000);
```
