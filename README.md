# Augur Socio-technical Data Framework

This repository contains a reproducible, sanitized snapshot of a project-level open-source software data framework. It connects aggregate metrics extracted from a local [Augur](https://oss-augur.readthedocs.io/) instance with socio-technical scores and CNCF project-status metadata, then produces the panel used for the socio-technical analysis.

The canonical analysis is [`analysis/03_sociotechnical_analysis.qmd`](analysis/03_sociotechnical_analysis.qmd). The other notebooks document the upstream extraction and data-construction steps.

## What is included

| Path | Purpose |
| --- | --- |
| `analysis/01_augur_extraction.qmd` | Extracts project-month aggregate Augur metrics from a local PostgreSQL/Docker deployment. |
| `analysis/02_build_sociotechnical_panel.qmd` | Joins Augur metrics, socio-technical scores, and CNCF metadata; creates forward-looking datasets. |
| `analysis/03_sociotechnical_analysis.qmd` | Primary regression and reporting analysis. |
| `data/` | Sanitized, aggregate project-level input and analysis data. See its README for details. |

Generated tables, plots, rendered documents, R workspace files, local configuration, and duplicate derived binaries are deliberately excluded from version control.

## Quick start: reproduce the socio-technical analysis

1. Install a recent [Quarto](https://quarto.org/docs/get-started/) release and R.
2. Install the R packages used by the analysis:

   ```r
   install.packages(c(
     "arrow", "dplyr", "easystats", "fixest", "flextable",
     "lubridate", "stringr", "tidyverse"
   ))
   ```

3. From the repository root, render the primary analysis:

   ```powershell
   quarto render analysis/03_sociotechnical_analysis.qmd
   ```

The notebook reads `data/panel_data.csv`; it will also perform the optional reconciliation when `data/predictive_dataset_6month_forward.csv` is present.

## Rebuild the data pipeline

The included CSV and Parquet files make the analysis reproducible without database access. Rebuilding upstream metrics requires a local Augur PostgreSQL database populated with the relevant projects.

1. Install the package set for the extraction and data-construction notebooks:

   ```r
   install.packages(c(
     "arrow", "DBI", "RPostgres", "dplyr", "easystats", "fixest",
     "ggplot2", "kableExtra", "lubridate", "modelsummary", "stringr",
     "tidyverse", "webshot2"
   ))
   ```

2. Set local-only connection variables. PowerShell example:

   ```powershell
   $env:AUGUR_DB_HOST = "localhost"
   $env:AUGUR_DB_PORT = "5432"
   $env:AUGUR_DB_NAME = "augur"
   $env:AUGUR_DB_USER = "augur"
   $env:AUGUR_DB_PASSWORD = "your-local-password"
   ```

   `.env.example` lists the same names, but it is a reference file only; the notebooks read the process environment and do not load `.env` automatically.

3. Render the notebooks in order:

   ```powershell
   quarto render analysis/01_augur_extraction.qmd
   quarto render analysis/02_build_sociotechnical_panel.qmd
   quarto render analysis/03_sociotechnical_analysis.qmd
   ```

The extraction notebook never inserts, lists, masks, or deletes GitHub tokens. Configure any required GitHub authentication within the local Augur/Docker deployment, outside this repository.

## Why there are two analysis-stage notebooks

They have separate roles. `02_build_sociotechnical_panel.qmd` constructs the forward-looking dataset and documents the data-model decisions; `03_sociotechnical_analysis.qmd` estimates and reports the final models using the supplied final panel. For readers who only want to reproduce the reported analysis, run notebook 03 alone. Keep notebook 02 when publishing the framework, because it documents and reproduces the upstream panel-construction step.

## Data scope and privacy

This release contains aggregate project-month measures only. It intentionally excludes raw commits, issues, pull requests, comments, and contributor records. Repository identifiers and public repository URLs are retained solely to support reproducible project-level joins. See [`data/README.md`](data/README.md) for the sanitization scope and file inventory.

## License and reuse

Unless a file states otherwise, this repository is licensed under [Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/). Reusers must give appropriate attribution, link to the license, indicate changes, and distribute adaptations under the same or a compatible license. The license does not itself grant rights to third-party data, trademarks, or privacy rights.

## Before publishing to GitHub

Run the local publication check below and review the staged diff before pushing:

```powershell
rg -n -i "ghp_|github_pat_|api[_-]?key|password\s*=|secret\s*=|[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}" .
```

No remote repository is created by this package. Create an empty GitHub repository, copy these files to its root, review the diff, and push with your normal institutional Git workflow.
