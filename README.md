# erddap

ERDDAP server configuration for CalCOFI (https://erddap.calcofi.io).

This repo is bind-mounted into the `erddap` service defined in
[`CalCOFI/server`](https://github.com/CalCOFI/server) `docker-compose.yml`, and
**must be cloned on the host** at the path that compose references:

```bash
git clone https://github.com/CalCOFI/erddap.git /share/github/CalCOFI/erddap
```

## Contents

- `content/setup.xml` — complete ERDDAP setup. Site-specific values
  (`baseUrl`, `baseHttpsUrl`, `admin*`, `flagKeyKey`, ...) are overridden at
  runtime by `ERDDAP_*` env vars in `CalCOFI/server`. A *minimal* setup.xml is
  rejected by ERDDAP 2.30.0 (it requires `categoryAttributes`, `admin*`,
  `accessConstraints`, `fees`, `keywords`, `flagKeyKey`, and logo settings).
- `content/datasets.xml` — dataset definitions (file paths resolve inside the
  container under `/datasets`, i.e. `/share/erddap/datasets/` on the host).
- `content/images/erddap2.css` — ERDDAP's own stylesheet slot (it looks for
  exactly this name under `images/`). The stock sample (`erddapStart2.css`)
  plus, appended, the `:root[data-theme="dark"]` overrides of the calcofi.io
  brand contract; light stays stock. Mounted into the webapp `images/` dir by
  `CalCOFI/server`, beside `calcofi.svg`.
- `content/images/calcofi.svg` — legacy header logo (the header now hotlinks
  the brand logo pair from calcofi.io).
- `messages.xml` — full copy of the ERDDAP default with only `<startHeadHtml5>`
  and `<startBodyHtml5>` customized; mounted over the classpath messages.xml by
  `CalCOFI/server` (a `content/erddap/messages.xml` does not work in 2.30.0).
  **Re-derive it from the new default on every ERDDAP upgrade**, then re-apply
  the two blocks. They carry the [calcofi.io brand contract](https://calcofi.io/brand/v2/):
  the head gets the favicon set, the pre-paint theme snippet, `theme.css` +
  `theme.js` and a real `<title>`; the body gets the `.cc-header` (logo ->
  calcofi.io, title -> this ERDDAP, links, 🌓 toggle) and the GA4 tag.

## Data locations (NOT in this repo)

ERDDAP's runtime state and source data files are kept outside the repo and are
mounted by `CalCOFI/server` `docker-compose.yml`:

| host path                  | container path | purpose                         |
|----------------------------|----------------|---------------------------------|
| `/share/erddap/data`       | `/erddapData`  | runtime state (logs, cache)     |
| `/share/erddap/datasets`   | `/datasets`    | source data files for datasets  |

Put source data files (NetCDF, CSV, ...) under `/share/erddap/datasets/` and
reference them from `content/datasets.xml` via `/datasets/...`.
