# @lensapp/mobx-utils

Utils for MobX.

## Usage

```ts
import { asyncComputed } from '@lensapp/mobx-utils';
import { autorun } from 'mobx';

const result = asyncComputed({
  getValueFromObservedPromise: () => fetchSomeData(),
  valueWhenPending: 'Loading...',
  betweenUpdates: 'show-latest-value',
});

autorun(() => {
  if (result.pending.get()) {
    console.log('Loading...');
  } else {
    console.log(result.value.get());
  }
});

// Invalidate the cached value and trigger a re-fetch
result.invalidate();
```

## Exports

### `asyncComputed(configuration)`

Creates a MobX computed value that resolves a promise reactively. Re-runs whenever observed MobX state accessed inside `getValueFromObservedPromise` changes.

**Parameters:**

- `getValueFromObservedPromise` — A function returning a `Promise<TValue>`. MobX observables accessed during this call are tracked; when they change the result is invalidated.
- `valueWhenPending` _(optional)_ — Value to expose via `.value` while the promise is pending. Defaults to `undefined`.
- `betweenUpdates` _(optional)_ — Controls what `.value` returns while a new promise is in-flight after an invalidation: `'show-pending-value'` shows `valueWhenPending`; `'show-latest-value'` keeps the previous resolved value.
- `equals` _(optional)_ — Custom equality comparer forwarded to the underlying MobX computed.

**Returns:** `IAsyncComputed<TValue, TPending>` with:
- `value` — `IComputedValue<TValue | TPending>` — the current resolved value or the pending placeholder.
- `pending` — `IComputedValue<boolean>` — `true` while the promise has not yet resolved.
- `invalidate()` — Discards the current cached value and schedules a re-fetch.

### `IAsyncComputed<TValue, TPending>`

Type returned by `asyncComputed`.
