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
- `content/images/calcofi.svg` — header logo.
- `messages.xml` — full copy of the ERDDAP default with only `<startBodyHtml5>`
  customized for the CalCOFI header; mounted over the classpath messages.xml by
  `CalCOFI/server` (a `content/erddap/messages.xml` does not work in 2.30.0).

## Data locations (NOT in this repo)

ERDDAP's runtime state and source data files are kept outside the repo and are
mounted by `CalCOFI/server` `docker-compose.yml`:

| host path                  | container path | purpose                         |
|----------------------------|----------------|---------------------------------|
| `/share/erddap/data`       | `/erddapData`  | runtime state (logs, cache)     |
| `/share/erddap/datasets`   | `/datasets`    | source data files for datasets  |

Put source data files (NetCDF, CSV, ...) under `/share/erddap/datasets/` and
reference them from `content/datasets.xml` via `/datasets/...`.
