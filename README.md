# Census Module POC: Employee › Plan Details

A small React app that runs the **production** Census "Employee › Plan Details" screens
(Balance Summary, Vesting and the Edit Vesting slide-over) from the `congruent-core-frontend`
master branch **on their own**, with no backend and no login.

Design target: Figma *Census* file, node `4622:78064`.

## Quick start

```bash
npm install --legacy-peer-deps
npm run dev
```

Open http://localhost:5173. It lands on Kevin Thomas › Mark Value Funding 401(K).
Click **Vesting**, then **Edit**, to see the slide-over.

## Where to look

| You want to… | Open |
|---|---|
| See Figma vs production side by side | [`docs/FIGMA-VS-PRODUCTION.md`](docs/FIGMA-VS-PRODUCTION.md) (or [`docs/figma-vs-production.html`](docs/figma-vs-production.html) in a browser) |
| Understand how the POC runs | [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) |
| See the only new code | [`src/poc/`](src/poc) |
| Change the sample data | [`src/poc/fixtures/index.js`](src/poc/fixtures/index.js) |

## Repository structure

```
.
├── README.md                     you are here
├── docs/
│   ├── ARCHITECTURE.md           how the POC runs, request flow, adding screens
│   ├── FIGMA-VS-PRODUCTION.md    side-by-side comparison, gaps, blockers, next steps
│   ├── figma-vs-production.html  same comparison as a styled page
│   └── screenshots/
│       ├── figma/                balance.png, vesting.png, edit.png  (from Figma)
│       └── production/           balance.png, vesting.png, edit.png  (from the POC)
├── public/
│   └── poc/                      employee photo and tenant logo from Figma
├── src/
│   ├── poc/                      ★ NEW: POC-only code (everything else is production)
│   │   ├── main.jsx              entry point: providers, router, the Plan Details page
│   │   ├── mockApi.js            serves every API call from fixtures
│   │   ├── fixtures/             sample data and permissions
│   │   ├── overrides/            stub login (AuthorizeService)
│   │   └── fa-shim/              Font Awesome Pro → Free icon mapping
│   ├── components/               production, unchanged
│   ├── containers/               production, unchanged
│   ├── layouts/                  production, unchanged
│   ├── hoc/ contexts/ services/  production, unchanged
│   ├── SDUI/ utils/ config/      production, unchanged
│   └── styles/ abstracts/ *.scss production global styles, unchanged
├── index.html                    loads src/poc/main.jsx
├── vite.config.mjs               adds the POC swaps (login stub, icon shim)
└── package.json
```

> Production folders keep their original paths so the code's `@/…` imports work
> unchanged. Only the files these screens import are included (546 modules).

## Production components on screen

- **Shell:** `hoc/withAppLayout` → `Header`, `Sidebar`, `SideBarContent`
- **Page:** `layouts/Employee/EmployeeManagePlansModules` → `containers/Employee/EmployeeManagePlansModulesContainer`
- **Balance Summary:** `BalanceSummary`, `BalanceSummarySourceTab`, `BalanceSummaryInvestmentTab`, `Piechartsummary`
- **Vesting:** `Vesting` → `CensusVestingCard` → `SliderPanel` with Formik fields (`FieldInput`, toggle, `FieldButtonGroup`, `FieldTextarea`)

## Summary of gaps

| Area | Figma | Production today |
|---|---|---|
| Page title | "← Back" + "Plan Details" | Notes icon only |
| Balance figures | 3 | 4, plus "Generate Statement" |
| Slide-over width | 600px | 80% of the screen |
| Navbar | Bell, help, avatar | User icon only |
| Icons | Font Awesome Pro | Free icons; 29 show a circle |

Full detail, blockers and next steps: [`docs/FIGMA-VS-PRODUCTION.md`](docs/FIGMA-VS-PRODUCTION.md).

## Findings for the master repo

- **Hidden Chart.js registration:** the Balance Summary pie only works because an unrelated module
  (`containers/ManageYearEndProcess/PieChart.jsx`) registers `ArcElement`. Loaded alone, the screen crashes.
  The POC registers it in `main.jsx`.
- **Undeclared Kendo dependencies:** `@progress/kendo-react-grid` 14.x needs `kendo-react-conversational-ui`,
  `kendo-react-upload`, `kendo-react-notification`, `kendo-common`, `kendo-csv`, `kendo-smartpaste-common`
  and `kendo-webspeech-common`, which `package.json` doesn't declare. They are added here.

## Using real Pro icons

Add your licensed `.npmrc`, install the `@fortawesome/pro-*` packages, and remove the three
Font Awesome aliases in `vite.config.mjs`.
