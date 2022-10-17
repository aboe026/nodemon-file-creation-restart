# nodemon-file-creation-restart

Minimal reproduceable repository to show nodemon not properly restarting on file creation in watch directory

## Prerequisites

- [NodeJS](https://nodejs.org/)
- [Yarn](https://yarnpkg.com/)
- [VSCode](https://code.visualstudio.com/)

  - To enable [Editor SDK](https://yarnpkg.com/getting-started/editor-sdks), run

    ```sh
    yarn dlx @yarnpkg/sdks vscode
    ```

    Then in TypeScript file, simultaneously press

    `ctrl` + `shift` + `p`

    and choose option

    `Select TypeScript Version`

    then select value

    `Use Workspace Version`

- [Install](#install)

## Install

To install dependencies, run

```sh
yarn install
```

## Steps to Reproduce

To produce the undesired behavior, run

```sh
yarn clean && yarn watch
```

You should see an error similar to

```
[10:42:44 AM] Starting compilation in watch mode...

D:\Repos\nodemon-file-creation-restart\.pnp.cjs:11981
  return Object.defineProperties(new Error(message), {
                                 ^

Error: Qualified path resolution failed: we looked for the following paths, but none could be accessed.

Source path: D:\Repos\nodemon-file-creation-restart\build\src\index.js
Not found: D:\Repos\nodemon-file-creation-restart\build\src\index.js

    at makeError (D:\Repos\nodemon-file-creation-restart\.pnp.cjs:11981:34)
    at resolveUnqualified (D:\Repos\nodemon-file-creation-restart\.pnp.cjs:12982:13)
    at resolveRequest (D:\Repos\nodemon-file-creation-restart\.pnp.cjs:12998:14)
    at Object.resolveRequest (D:\Repos\nodemon-file-creation-restart\.pnp.cjs:13054:26)
    at resolve$1 (file:///D:/Repos/nodemon-file-creation-restart/.pnp.loader.mjs:234:25)
    at ESMLoader.resolve (node:internal/modules/esm/loader:422:30)
    at ESMLoader.getModuleJob (node:internal/modules/esm/loader:222:40)
    at ESMLoader.import (node:internal/modules/esm/loader:276:22)
    at node:internal/modules/run_main:51:28
    at loadESM (node:internal/process/esm_loader:88:11)
[nodemon] app crashed - waiting for file changes before starting...
[10:42:45 AM] Found 0 errors. Watching for file changes.
```

And you will notice that nodemon does not ever restart, even after the `watch-tsc` script that was executed in parallel finishes (and creates the `build/src/index.js` file).

## Workaround

The current workaround being used to get past this bug can be used by doing the following:

1. Run `yarn build` before the `yarn watch` command like so:

   ```sh
   yarn clean && yarn build && yarn watch
   ```

This does not produce an error because the workaround ensures that the built file nodemon is configured to exec against is present beforehand.
Ideally, nodemon should automatically restart after the `build/src/index.js` file is created by the parallel `watch-tsc` finishes.
