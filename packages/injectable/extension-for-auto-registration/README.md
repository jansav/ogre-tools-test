# `@lensapp/injectable-extension-for-auto-registration`

Auto-registers injectables from default exports of files that match a `require.context`.

## Installation

```
npm install @lensapp/injectable
npm install @lensapp/injectable-extension-for-auto-registration
```

## Usage

```js
import { createContainer } from '@lensapp/injectable';
import autoRegister from '@lensapp/injectable-extension-for-auto-registration';

const di = createContainer('my-container');

autoRegister({
  di,
  targetModule: module,
  getRequireContexts: () => [
    require.context('./some-directory', true, /\.injectable\.(ts|tsx|js)$/),
    require.context('./some-other-directory', true, /\.injectable\.(ts|tsx|js)$/),
  ],
});
```

## API

### `autoRegister({ di, targetModule, getRequireContexts })`

Scans all files returned by the require contexts and registers every injectable (or injectable bunch) found as an export.

| Parameter | Type | Description |
|---|---|---|
| `di` | `DiContainer \| DiContainerForInjection` | The DI container to register injectables into |
| `targetModule` | `Module` | The current module object (pass `module` in Webpack/Node context). Used to polyfill `require.context` in non-Webpack environments. |
| `getRequireContexts` | `() => RequireContext[]` | A callback returning an array of `require.context()` calls that resolve to the injectable files |

Throws if no matching files are found, or if a matched file exports no injectables.
