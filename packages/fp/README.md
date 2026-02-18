# @lensapp/fp

A collection of low abstraction functions in functional programming flavor.

## Usage

```
$ npm install @lensapp/fp

...

import { pipeline, flow, mapValuesDeep } from '@lensapp/fp';
```

## Exported API

| Export | Description |
|---|---|
| `awaitAll` | Alias for `Promise.all` — resolves an array of promises or mixed values in parallel. |
| `flow` | Composes multiple functions into a left-to-right pipeline; handles async/await and supports early exit via `pipelineBreak`. |
| `pipelineBreak` | Token value used with `flow` to stop execution early and return the current value. |
| `pipeline` | Data-first variant of `flow` — takes a value as the first argument followed by transformer functions. |
| `safePipeline` | Like `pipeline` but stops execution when any function returns `undefined`. |
| `mapValuesDeep` | Recursively maps over all values in nested objects and arrays; supports async mappers and handles circular references. |
| `isPromise` | Returns `true` if the value is a native `Promise` instance (not a generic thenable). |
| `isPrimitive` | Returns `true` for primitive values (`string`, `number`, etc.); `false` for objects, arrays, and promises. |
| `getFrom` | Retrieves a property from an object by key; returns `undefined` if the key does not exist. |
| `getSafeFrom` | Retrieves a property from an object by key; throws a descriptive error listing available keys if the key does not exist. |
| `firstMatchValue` | Returns the first non-`undefined` result from applying an array of functions to a value, in priority order. |
| `DeepMap` | A nested `Map` data structure that supports multi-level key composition with `get`, `set`, and `has` operations. |
| `replaceTagsWithValues` | Replaces `{tag}` placeholders in a string with values from an object, recursively resolving nested tags; throws on missing values. |
| `replaceTagsWithValuesUnsafe` | Like `replaceTagsWithValues` but silently leaves unresolved tags in place instead of throwing. |
