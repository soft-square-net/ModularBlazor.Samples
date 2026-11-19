# ModularBlazor.Samples

Small demo modules for the [ModularBlazor](https://github.com/soft-square-net/ModularBlazor) platform. Each one shows one way to build a module and is
released as its own bundle; they share a repo because they are examples, not products.

> **Status: scaffold.** The samples still live in the platform repo. They move here, and are removed from the platform
> in the same step, once the platform publishes its contract packages.

| Sample | Shows | In the platform today |
| --- | --- | --- |
| Greeting | the smallest host module (one project, `IRuntimeModule` + endpoints) and its API client | [`src/WebApi/Modules/Custom/WebApi.Modules.Greeting`](https://github.com/soft-square-net/ModularBlazor/tree/main/src/WebApi/Modules/Custom/WebApi.Modules.Greeting) and `.Client` |
| Inventory | a layered module (Domain, Application, Infrastructure) storing data in each tenant's own database, and its API client | [`src/WebApi/Modules/Custom/Inventory`](https://github.com/soft-square-net/ModularBlazor/tree/main/src/WebApi/Modules/Custom/Inventory) |
| Pulse | a host module with a background service and its own ASP.NET Core pipeline | [`src/WebApi/Modules/Custom/WebApi.Modules.Pulse`](https://github.com/soft-square-net/ModularBlazor/tree/main/src/WebApi/Modules/Custom/WebApi.Modules.Pulse) and `.Client` |
| Dashboard | a client-only module (a .dll), lazy loaded | [`modules/client/Module.Dashboard`](https://github.com/soft-square-net/ModularBlazor/tree/main/modules/client/Module.Dashboard) |
| Reports | a client-only module shipped as a .nupkg | [`modules/client/Module.Reports`](https://github.com/soft-square-net/ModularBlazor/tree/main/modules/client/Module.Reports) |

Dashboard and Reports are seeded by the platform today as core client modules; once they move here they become
ordinary installable samples.

## Layout

```
samples/<Name>/   one folder per sample, each with its own src/, playground/ and module.json
```

## How it builds and ships

- It compiles against the platform's contract packages (`WebApi.Contracts`, `ModularBlazor.Abstractions`, ...) from the
  soft-square-net GitHub Packages feed. It never references platform source.
- CI packs one bundle per release: `<module>-<version>.modpkg`, a zip with `module.json`, `server/`, `client/` and
  `contracts/`. The platform's admin area installs it: server part first (tenant migrations), then the client part.
- One version for the whole bundle, set once from the git tag. Unchanged parts are skipped on install.
- An API client ships once, inside the bundle that owns the API, as a hidden client module. Modules that call it
  reference its package with `ExcludeAssets="runtime"` and declare a dependency with a version range in `module.json`.

## Developing

`playground/` will hold a small Aspire AppHost: the platform's containers (gateway, WebApi, identity, Blazor client)
plus this repo's projects, loaded from their build output, so F5 shows a change without packing or uploading.

## Branches

Trunk-based: `main` always produces an installable bundle; short-lived `feat/...` and `fix/...` branches; a release
is a tag such as `v1.2.0`.
