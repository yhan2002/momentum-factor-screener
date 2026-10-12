# US Large-Cap Momentum Screener

A small Python/Jupyter research workflow for ranking a fixed list of 40 U.S. large-cap stocks using adjusted closing prices, momentum, and an annualized return-to-volatility ratio.

## Current scope

This is a research notebook, with no broker connection, order execution, portfolio-account records, scheduler, or production service. The ticker list is defined in the notebook; it is not the complete or point-in-time S&P 500 universe.

- Prices come from Yahoo Finance through `yfinance`, starting at `2024-01-01` and ending at the provider's latest available data when run.
- Columns with any missing price are dropped. This can change the retained universe.
- `Momentum_60D` is the existing calculation `last price / price at iloc[-60] - 1`: the endpoints span 59 return intervals. The label and calculation have not been changed.
- `Sharpe_Ratio` uses daily mean return divided by daily sample standard deviation, multiplied by `sqrt(252)`, with a zero risk-free-rate assumption over the downloaded history.
- Results are ranked by momentum. This is not a backtest and does not include fees, slippage, or a historical membership universe.

## Files

| Path | Purpose |
| --- | --- |
| [`momentum_factor.ipynb`](momentum_factor.ipynb) | Nine code cells: download, calculate, rank, plot, and export. |
| [`requirements.txt`](requirements.txt) | Python dependency lower bounds; not a locked environment. |
| [`output/momentum_ranking.csv`](output/momentum_ranking.csv) | Saved research ranking from a previous run. |
| [`output/momentum_chart.png`](output/momentum_chart.png) | Saved research chart from a previous run. |

The saved outputs are examples, not live recommendations or account holdings. Their chart title mentions the S&P 500, but only the fixed notebook ticker list is evaluated.

![Saved momentum research chart](output/momentum_chart.png)

## Environment and execution

Use Python with the packages in `requirements.txt`. A typical fresh setup is shown below; a clean installation and a live Yahoo download were not performed in the latest review.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m jupyter notebook momentum_factor.ipynb
```

Open the notebook from the repository root and run its cells in order. The ticker list and download start date are in cells 2 and 3. Running the download contacts Yahoo Finance. The export cells create `output/` if needed and overwrite its CSV and PNG; copy any results you want to keep before rerunning.

No API key or account credential is required by the current notebook. Do not add one to a notebook cell or saved output.

## Validation

This source-only check does not download data or modify saved results:

```powershell
python -c "import ast,json,pathlib; n=json.loads(pathlib.Path('momentum_factor.ipynb').read_text(encoding='utf-8')); [ast.parse(''.join(c['source'])) for c in n['cells'] if c['cell_type']=='code']; print('Notebook JSON and Python syntax OK')"
git diff --check
```

On 2026-10-12, all nine code cells passed syntax and notebook-schema checks and executed with synthetic prices, network access blocked, and exports redirected to a temporary directory. The resulting 40-row ranking and chart were checked. This verifies the offline calculation/export path, not current Yahoo responses or market results. There is no committed automated test suite or CI workflow.

The review used an existing Python 3.14 environment. A dependency dry-run confirmed the numeric/download libraries but could not resolve the `jupyter` metapackage with offline package lookup; it does not establish that a fresh installation succeeds.

## Privacy before publishing

Review notebook source, outputs, metadata, charts, CSVs, and the staged diff before committing. Keep account records, credentials, personal paths, and contact details out of published files. Local environment files, logs, virtual environments, and notebook checkpoints are ignored; existing tracked files and Git history are not erased by ignore rules.

Commit author and committer information is also public in this repository. Use the GitHub-provided private commit email if you do not want a personal address exposed. Changing future commit identity does not remove older metadata.
