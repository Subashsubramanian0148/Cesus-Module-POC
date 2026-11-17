# How the POC works

The goal is to run real production screens on their own: no backend, no login, no component edits.

```
index.html
  └─ src/poc/main.jsx                  POC entry point
       ├─ src/poc/mockApi.js           replaces axios' network layer with local fixtures
       ├─ production providers         Chakra theme, AppLayout, Global, ManageCensus contexts
       └─ production page
            PersistentAppLayoutContainer  (Header + Sidebar)
              └─ EmployeeManagePlansModules
                   ├─ BalanceSummary → Piechartsummary, source / investment tabs
                   └─ Vesting → CensusVestingCard → SliderPanel (Edit form)
```

## Request flow

1. A production component calls a production service, for example `GetEmployeeVestingDetails`.
2. The service uses the shared axios instance in `src/services/api.js` (unchanged).
3. `src/poc/mockApi.js` has replaced axios' adapter, so the call never leaves the browser.
4. The URL is matched against `src/poc/fixtures/index.js` and the fixture data is returned.
5. Calls with no fixture return an empty result and log `[POC mock] no fixture for …` in the console.

## Build-time swaps (`vite.config.mjs`)

| Swap | Why |
|---|---|
| `AuthorizeService` → `src/poc/overrides/AuthorizeService.jsx` | Skips the real OIDC login; returns a fixed admin user |
| `@fortawesome/pro-*` → `src/poc/fa-shim/` | Pro icons need a licence; Free icons of the same name are used instead |

## How the file set was chosen

Only files the screens actually import are included. A production build was traced
(Vite `getModuleIds()`) and every `src/` module it pulled in was kept: 546 modules,
plus the global `src/styles` and `src/abstracts` SCSS. The other ~36 MB of the app was left out.

## Adding another screen

1. Add a `<Route>` for the production page in `src/poc/main.jsx`, using the same URL shape as production
   (route permissions are looked up by URL).
2. Open it and watch the console for `[POC mock] no fixture for …`.
3. Add each missing endpoint to `src/poc/fixtures/index.js`.
4. If the page imports production files that aren't in this repo yet, copy them in at the same path.
