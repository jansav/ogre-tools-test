# `@lensapp/injectable-extension-for-mobx`

MobX integration for `@lensapp/injectable` — adds reactive inject-many utilities backed by MobX computed values.

## Installation

```
npm install @lensapp/injectable
npm install @lensapp/injectable-extension-for-mobx
```

## Usage

```js
import { createContainer } from '@lensapp/injectable';
import {
  registerMobX,
  computedInjectManyInjectionToken,
} from '@lensapp/injectable-extension-for-mobx';

const di = createContainer('my-container');

// Register MobX support into the container once at startup.
registerMobX(di);

// Inject the computedInjectMany utility and use it to reactively observe all
// injectables registered for a given token, including ones registered later.
const computedInjectMany = di.inject(computedInjectManyInjectionToken);
const items = computedInjectMany(someInjectionToken); // IComputedValue<T[]>
```

## API

### `registerMobX(di)`

Registers all MobX-related injectables (such as `computedInjectManyInjectable`) into the container. Must be called once before using any computed inject utilities.

| Parameter | Type | Description |
|---|---|---|
| `di` | `DiContainer` | The DI container to register MobX support into |

### Exported injectables and injection tokens

| Export | Type | Description |
|---|---|---|
| `computedInjectManyInjectable` | `Injectable<ComputedInjectMany>` | Injectable that provides `computedInjectMany`. Registered automatically by `registerMobX`. |
| `computedInjectManyInjectionToken` | `InjectionToken<ComputedInjectMany>` | Injection token for obtaining `computedInjectMany` via `di.inject`. |
| `computedInjectManyWithMetaInjectable` | `Injectable<ComputedInjectManyWithMeta>` | Injectable that provides `computedInjectManyWithMeta`. Registered automatically by `registerMobX`. |
| `computedInjectManyWithMetaInjectionToken` | `InjectionToken<ComputedInjectManyWithMeta>` | Injection token for obtaining `computedInjectManyWithMeta` via `di.inject`. |
| `computedInjectMaybeInjectionToken` | `InjectionToken<ComputedInjectMaybe>` | Injection token for obtaining `computedInjectMaybe` via `di.inject`. Returns `IComputedValue<T \| undefined>` — throws if more than one registration is found. |

### Utility signatures (obtained via injection)

**`computedInjectMany(token, ?param)`** → `IComputedValue<T[]>`
Returns a MobX computed value that reactively tracks all instances registered under `token`. Re-evaluates when new injectables are registered or deregistered at runtime.

**`computedInjectManyWithMeta(token, ?param)`** → `IComputedValue<InjectionInstanceWithMeta<T>[]>`
Same as `computedInjectMany` but each entry also carries the injectable id as metadata.

**`computedInjectMaybe(token, ?param)`** → `IComputedValue<T | undefined>`
Reactive single-value variant. Returns `undefined` when nothing is registered, the instance when exactly one is registered, and throws when more than one is registered.
