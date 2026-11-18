# Trade Reconciliation: Summit Capital

Reconciliation of 400+ equity trades between a firm's internal trade records and its custodian's statement. The project finds every break, measures the money at risk and how long each break has been open, and traces the root causes.

*Summit Capital is fictional, and all data is synthetic.*

## What it does
- Matches internal trades to custodian records in **SQL** (SQLite), with price and amount tolerances so rounding differences don't count as breaks
- Classifies breaks: missing on either side, duplicate bookings, wrong side, quantity, price and amount mismatches, and failed, past-due and late settlements
- Calculates the dollar exposure and age of each break, and assigns an escalation priority
- Exports a break report for a **Power BI** dashboard

## Results
| Metric | Result |
|---|---|
| Trades reconciled | 402 |
| Auto-match rate | 91.5% |
| Breaks found | 37 (33 open, 4 resolved late settlements) |
| Open exposure | $3.9M |

- **Root cause:** Dune Brokerage caused **43% of breaks (16 of 37)**, more than twice its 20% fair share. Most were price mismatches, quantity mismatches and trades missing at the custodian. These are booking and confirmation errors, not settlement failures.
- **Biggest risk:** SC-000010, a quantity mismatch (3,000 shares booked vs 300 settled, likely a manual entry error) with **$1.19M of exposure**, open **45 days**. Escalated to the manager and trading desk, with the correct quantity to be confirmed with Dune.
- **Control gap:** Summit's own books were wrong too: 2 trades were booked twice and 3 settled trades were never booked.
- **Recommendation:** Same-day trade confirmation with Dune, which would have caught about 10 of its 16 breaks before settlement, plus running the reconciliation daily so breaks like SC-000010 are flagged on day one rather than day 45.

## Why tolerance matters
Summit records prices to 4 decimals and the custodian reports 2. An exact price comparison flags **391 of 402 trades**, almost all of them rounding noise. A $0.01 tolerance cuts this to the 5 real price breaks.

## How to run
1. Open `trade_reconciliation.ipynb` in Jupyter (Anaconda Navigator → Jupyter Notebook).
2. Run all cells. Outputs are written to `outputs/`.

## Files
| Path | Contents |
|---|---|
| `data/internal_trades.csv` | Summit's booked trades |
| `data/custodian_records.csv` | Custodian statement |
| `trade_reconciliation.ipynb` | SQL matching, break report, charts |
| `outputs/` | Break report CSVs for Power BI |
| `answer_key/` | Expected breaks, for self-checking |
