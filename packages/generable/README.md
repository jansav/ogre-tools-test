# @lensapp/generable

A collection of low abstraction functions in functional programming flavor for generators and async iterables.

## Usage

```
$ npm install @lensapp/generable

...

import { range, map, asArray } from '@lensapp/generable';
```

## Exported API

Functions are grouped by role.

### Sources — create iterables

| Export | Description |
|---|---|
| `range` | Generates a finite arithmetic sequence from a start value with a specified step and count. |
| `infinity` | Generates an infinite sequence starting from a seed value, applying a reducer function on each step. |
| `manualTrigger` | Creates a manually-controlled async iterable with `yield` and `return` methods to push values on demand. |

### Publishers — wrap iterables for reuse

| Export | Description |
|---|---|
| `asStream` | Converts an iterable into a lazy async iterable with back-pressure handling. |
| `asSubscribable` | Wraps an iterable as a subscribable object whose `subscribe` method delivers buffered values to each subscriber. |
| `asValue` | Creates an async iterable that buffers values and can be consumed multiple times, always reflecting the latest yielded value. |

### Slackers — transform iterables

| Export | Description |
|---|---|
| `map` | Transforms each element using a mapper function or static value; supports sync/async iterables and async mappers. |
| `filter` | Yields only elements that satisfy a predicate function; supports sync and async iterables. |
| `take` | Yields only the first N elements from an iterable, then stops. |
| `chunk` | Yields fixed-size arrays of consecutive elements from an iterable. |
| `scan` | Yields each intermediate accumulated value as it processes the iterable (like `reduce` but streaming). |
| `tap` | Executes a side-effect function for each value while passing the value through unchanged. |
| `debounce` | Yields values with debouncing — only emits after a specified idle delay. |
| `regulate` | Inserts a fixed delay between each emitted value to rate-limit iteration. |
| `asAsync` | Converts a synchronous iterable into an async generator. |

### Pullers — consume iterables

| Export | Description |
|---|---|
| `asArray` | Collects all values from an iterable into an array. |
| `reduce` | Applies a reducer function across an iterable to produce a single accumulated value. |
| `forEach` | Iterates through each value in an iterable, executing a callback for each item. |
| `buffer` | Creates a buffered async iterable that supports multiple concurrent consumers with a configurable buffer size. |

### Weavers — combine multiple iterables

| Export | Description |
|---|---|
| `parallelize` | Merges multiple iterables by consuming them concurrently and yielding values as they arrive. |
| `serialize` | Combines multiple iterables into a single iterable that yields values one at a time using `Promise.race`. |
| `unravel` | Distributes values from a single iterable across multiple output iterables. |
