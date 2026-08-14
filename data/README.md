# Data package

All included records are project- and month-level aggregates. The package excludes raw commit, issue, pull-request, comment, and contributor records.

| File | Role |
| --- | --- |
| `cncf_sociotechnical_scores.parquet` | Input socio-technical scores used by the extraction and modeling workflow. |
| `projects.csv` | CNCF project-level status metadata used to derive maturity controls. |
| `augur_monthly_metrics.csv` | Project-month aggregate metrics extracted from Augur. |
| `predictive_dataset_6month_forward.csv` | Six-month look-ahead joined dataset created by the socio-technical modeling notebook. |
| `panel_data.csv` | Final project-month analysis panel used by the socio-technical analysis notebook. |

## Sanitization

The release contains no raw user/contributor tables. Before packaging, the scripts and CSV files were scanned for email addresses, common GitHub personal-access-token formats, credential assignments, and URL user-info. No matches were retained. Project repository names, identifiers, and public repository URLs remain because they are necessary for reproducible joins and project-level interpretation.

The included data are point-in-time extracts. Re-running the collection notebook against a different Augur database or at a later date can produce different values.
