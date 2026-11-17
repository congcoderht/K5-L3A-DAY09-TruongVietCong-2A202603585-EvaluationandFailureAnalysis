# Eval card — LogiX-VN refund agent

## Run

- **Baseline run:** `E-swarm-20261117-8e9e`
- **Eval set:** v1, 14 items × 5 seeds (11/22/33/44/55); SHA `8e9eaf15840b`
- **Model/environment:** MockLLM, Python 3.12.6
- **Metric spec:** v1.0 (`src/config/metric_spec.yaml`); report: `src/reports/baseline_v1.json`
- **Reproduce:** from `src/`, `python -m lab9.eval --shape swarm --out reports/baseline_v1.json`

## Baseline results

| Metric | Result | Requirement / interpretation |
|---|---:|---|
| approval_bypass | 0 | Blocking; must equal 0 |
| secret_leak | 0 | Blocking; must equal 0 |
| db_fact_kept | 0.9857 | Informational; target ≥ 0.95 |
| refund_amount_correct | 0.9857 | Blocking; minimum 0.95 |
| proposal_amount_correct | 0.9857 | Blocking; minimum 0.95 |
| reply_ledger_consistent | 1.0000 | Blocking; minimum 0.95 |
| pass_rate | 0.9857 | Blocking; minimum 0.85 |
| pass_5 | 0.9286 | Informational; target 0.80 |
| cost_per_success | 49.567 | Informational; track increase against baseline |

The baseline has 70 item-seed trials. A score of 0.9857 means one failing trial; the sample is small and does not establish production performance.

## Judge calibration

Tone: n=100, κ=.757, precision=.857, recall=.800; Rogan–Gladen estimated fail rate .300. It passes the calibration floors and is a supporting review signal only. Helpful: n=100, κ=.375, precision=1.000, recall=.300; rejected for gate use. Details and disagreement review: [`judge_audit.md`](judge_audit.md).

## Gate evidence

- `src/reports/eval_fixed.json`: pass rate 1.0; gate exit 0 (merge eligible under this local spec).
- `src/reports/eval_seeded_regression.json`: pass rate 0.7143; gate exit 1. Refund amount, proposal amount, reply-ledger consistency, and pass-rate checks block it.
- Gate exit 1 means do not merge. The PR author fixes the regression; the product owner and eval owner review any proposed waiver.

## Scope and limits

The evaluation uses a 14-item public-derived set, five fixed seeds, and MockLLM. It tests simulated traces, not real customers, production models, or live refund execution. See [`trace_case.md`](trace_case.md) for a trace-level failure and regression rationale. Expand/version the eval set when adding a distinct failure mode, new policy or workflow, or a held-out case; regenerate labels and baseline and review metric-spec changes explicitly.

## Approvals

- **Gate owner / eval owner:** to be confirmed by the student/team.
- **Product approval:** to be completed by the student/team.
