
---
name: strategy-comparison
description: Compare investment strategies with governed comparability, deterministic multi-criteria scoring, scenario robustness, investor-goal screening, explainable reporting, and versioned integration contracts.
version: 1.0.0
status: design-approved-implementation-pending
---

# Skill #9 — Strategy Comparison

## 1. Mission and boundaries

Compare two or more candidate investment strategies fairly, evaluate risk and performance trade-offs, screen against investor constraints, and produce transparent, reproducible evidence for Skill #10 — Decision Support. Operate independently on validated CSV/JSON inputs or consume versioned outputs from Skills #1–#8. All mathematical scores, exclusions, and rankings must be computed deterministically in Python. Optional AI may explain immutable calculated results but must not change them.

This is an educational analytical system, not a licensed financial-advice or guaranteed-return service. No market returns, scenario probabilities, or investor attributes may be invented. Missing essential inputs yield `undetermined` or blocked results.

## 2. Approved design decisions

1. **Comprehensive Strategy Intelligence:** Compare return, risk, drawdown, liquidity, goal probabilities, retirement sustainability, historical/forward results, scenarios, and robustness.
2. **Hybrid Strategy Intelligence Architecture:** Support standalone CSV/JSON and versioned upstream agent outputs; preserve provenance and data compatibility.
3. **Multi-Dimensional Comparison & Fairness Governance:** Normalize valid differences; classify comparisons as `comparable`, `conditionally_comparable`, or `not_comparable`; block unsupported rankings.
4. **Comprehensive Multi-Criteria Decision Analysis:** Weighted scoring, TOPSIS, Pareto analysis, hard-constraint screening, ranking sensitivity, and explainable metric contributions.
5. **Scenario Intelligence & Robustness:** Historical, simulated, and hypothetical scenarios; scenario-specific rankings, regret, worst-case analysis, and documented probabilities only.
6. **Investor-Centered Suitability & Goal Intelligence:** Multi-goal assessment, risk willingness versus capacity, cash-flow phases, hard constraints, probabilistic goals, and `undetermined` when data are insufficient.
7. **Interactive Strategy Intelligence & Reporting:** Dashboard, charts, score breakdowns, interactive preferences, and HTML/PDF/JSON/CSV exports with audit metadata.
8. **Statistical Validation, Governance & Auditability:** Tiered findings, cross-skill reconciliation, statistical controls, reproducible configurations, and traceable transformations.
9. **Hybrid Multi-Agent Orchestration:** Standalone deterministic Python execution plus optional orchestration and AI explanations; no OpenAI key needed for core workflows.
10. **Multi-Gate Verification & Professor Acceptance:** Six engineering gates from contracts to hosted, independent professor validation.

## 3. Scope and dependency map

| Source | Expected contribution | Dependency policy |
|---|---|---|
| Skill #1 — Investor Profile | Goals, horizons, risk tolerance/capacity, liquidity, constraints | Optional for generic comparisons; required for investor-specific suitability |
| Skill #2 — Market Data | Return histories, benchmarks, risk-free rates, source dates | Optional when validated comparable metrics are supplied |
| Skill #3 — Portfolio Optimization | Candidate allocations, optimization assumptions, frontier data | Optional |
| Skill #4 — Risk Assessment | Volatility, drawdowns, VaR/CVaR, concentration, downside risk | Optional if corresponding validated metrics supplied |
| Skill #5 — 3D Visualization | Optional visualization handoff | Not a mathematical input dependency |
| Skill #6 — Historical Backtesting | Historical strategy performance, fees, periods, data lineage | Optional |
| Skill #7 — Forward Testing & Paper Trading | Out-of-sample/paper-trading metrics and dates | Optional |
| Skill #8 — Monte Carlo | Goal probabilities, depletion, stress results, simulation metadata | Optional; required for Monte Carlo-based claims |
| Skill #10 — Decision Support | Consumes validated comparison result | Downstream |

Do not assume upstream schemas exist or match this document until separately verified. Build explicit adapters with version checks; never infer incompatible fields silently.

## 4. Input contract — `strategy_comparison.request.v1`

The versioned JSON request has the following logical structure. This is an illustrative contract instance, **not** a published JSON Schema file:

```json
{
  "schema_version": "strategy_comparison.request.v1",
  "request_id": "comparison-demo-001",
  "as_of_date": "2026-11-17",
  "base_currency": "USD",
  "input_mode": "standalone",
  "evaluation": {
    "start_date": "2021-01-01",
    "end_date": "2025-12-31",
    "return_frequency": "monthly",
    "return_convention": "arithmetic_simple",
    "performance_basis": "net_of_fees",
    "benchmark_id": "BENCHMARK-01",
    "risk_free_rate_annual": 0.03,
    "inflation_basis": "nominal"
  },
  "strategies": [
    {
      "strategy_id": "A",
      "label": "Growth",
      "metrics": {
        "annualized_return": 0.12,
        "annualized_volatility": 0.20,
        "max_drawdown": -0.35
      },
      "provenance": {"source_type": "illustrative", "source_id": "example-a"}
    },
    {
      "strategy_id": "B",
      "label": "Balanced",
      "metrics": {
        "annualized_return": 0.09,
        "annualized_volatility": 0.11,
        "max_drawdown": -0.18
      },
      "provenance": {"source_type": "illustrative", "source_id": "example-b"}
    }
  ],
  "criteria": [
    {"metric": "annualized_return", "direction": "maximize", "weight": 0.4},
    {"metric": "annualized_volatility", "direction": "minimize", "weight": 0.3},
    {"metric": "max_drawdown", "direction": "maximize", "weight": 0.3}
  ],
  "scoring": {"method": "weighted", "normalization": "min_max", "tie_tolerance": 1e-9},
  "constraints": [],
  "scenarios": [],
  "goals": [],
  "governance": {
    "missing_metric_policy": "block_affected_ranking",
    "comparability_policy": "strict",
    "calculation_version": "1.0.0"
  }
}
```

**Required at runtime:** schema version, request ID, as-of date, base currency, at least two unique strategies, evaluation conventions, criteria, scoring configuration, and governance. Strategy metrics must include each scored criterion or the affected ranking is blocked. Investor-specific outputs require a validated investor profile and relevant goals/constraints. Scenario probabilities are optional and may only be used if documented and validated. Examples are illustrative and not market observations.

**CSV import:** Use a documented manifest for currency, dates, frequency, units, net/gross treatment, provenance, and strategy identifiers; CSV alone must not imply missing metadata. Reject ambiguous percentage-versus-decimal units.

**Validation:** JSON Schema enforces structural fields/types/enums; Python semantic checks enforce cross-field consistency, unique identifiers, weights, comparability, chronology, financial conventions, and data sufficiency. Version adapters are explicit, tested, and auditable.

## 5. Output contract — `strategy_comparison.result.v1`

Return a structured result containing:

```json
{
  "schema_version": "strategy_comparison.result.v1",
  "request_id": "comparison-demo-001",
  "status": "completed",
  "comparability": {
    "classification": "comparable",
    "reconciliations": [],
    "limitations": []
  },
  "validation": {"critical": [], "warnings": [], "information": []},
  "eligibility": [
    {"strategy_id": "A", "status": "eligible", "reasons": []},
    {"strategy_id": "B", "status": "eligible", "reasons": []}
  ],
  "rankings": [],
  "scores": [],
  "pareto_fronts": [],
  "scenario_results": [],
  "goal_results": [],
  "robustness": {},
  "explanations": [],
  "provenance": {
    "calculation_version": "1.0.0",
    "input_digest": "computed-at-runtime",
    "source_records": []
  }
}
```

The empty arrays in this example are **placeholders**, not a claim that comparison calculations were executed. A `completed` runtime result must include the applicable calculated scores and rankings; otherwise return `partial`, `blocked`, or `failed` with explicit reasons. Never label a blocked or uncomputed comparison as completed.

Ranking records must identify method, rank, strategy ID, score, tie status, eligibility, metric contributions, and any qualification. All output metrics must include units and time basis in the schema or associated metadata. Export exact unrounded machine-readable values and separately formatted display values.

## 6. Six-phase execution workflow

1. **Ingest:** Read standalone CSV/JSON or versioned upstream outputs; register provenance and immutable raw-input digest.
2. **Validate and reconcile:** Apply schema and semantic checks; align dates, currencies, frequencies, benchmarks, cash-flow assumptions, fees, and inflation basis only where defensible. Record each transformation.
3. **Screen:** Apply hard constraints, data sufficiency, and suitability rules; mark `eligible`, `ineligible`, or `undetermined` with reasons.
4. **Compare and rank:** Compute supported metrics, weighted scores, TOPSIS, Pareto fronts, ties, and sensitivity. Do not rank noncomparable or ineligible strategies in the eligible leaderboard.
5. **Stress and explain:** Evaluate supported scenarios, robustness, regret, goal outcomes, and ranking stability; distinguish known results from unsupported estimates.
6. **Publish:** Produce structured result, audit trail, dashboard payload, exports, and optional immutable-evidence AI interpretation; hand off to Skill #10.

## 7. Comparison fairness rules

- Require matching evaluation windows for direct historical performance rankings; a common-period recomputation is preferred to comparing different windows.
- Normalize return frequency using mathematically valid methods and underlying return series when required; do not equate arithmetic and geometric returns.
- Distinguish time-weighted returns from money-weighted returns; reconcile cash-flow treatment before comparison.
- Match gross/net treatment, fee inclusion, tax treatment, currency, inflation basis, and benchmark definitions.
- Report whether strategy returns are backtested, out-of-sample, paper-traded, simulated, or live. Never combine them as if they had identical evidentiary status.
- Do not compare goal probabilities from different assumptions or simulation horizons without explicit reconciliation.
- `comparable`: all material assumptions align. `conditionally_comparable`: permissible documented reconciliation/qualification. `not_comparable`: material mismatch prevents the affected ranking.
- No invented missing metrics, investor preferences, probability weights, or source histories.

## 8. Deterministic scoring mathematics

### 8.1 Weighted composite score

For eligible strategies `i`, criteria `j`, and nonnegative weights `w_j` with `sum(w_j)=1`:

`S_i = sum_j(w_j * z_ij)`

where `z_ij` is a documented normalized benefit score. Default min-max normalization on the eligible comparison cohort:

- Benefit: `z_ij = (x_ij - min_j) / (max_j - min_j)`.
- Cost: `z_ij = (max_j - x_ij) / (max_j - min_j)`.
- Constant criterion: assign the same neutral score to every eligible strategy and flag zero discrimination; never divide by zero.

Normalize only across comparable data. Missing required criteria block the affected composite ranking unless an explicitly approved, disclosed alternate metric set is used. Cohort-relative normalization can change when strategies are added; report this limitation. Do not double-count highly correlated criteria without disclosure.

### 8.2 TOPSIS

For eligible matrix `X`, use vector normalization `r_ij = x_ij / sqrt(sum_i x_ij^2)` for each criterion with nonzero norm; then `v_ij = w_j * r_ij`. Determine ideal best/worst by criterion direction. Calculate Euclidean distances `D_i+`, `D_i-` and closeness:

`C_i = D_i- / (D_i+ + D_i-)`

Rank higher `C_i` first. For a zero denominator, mark a tie and emit a diagnostic. Zero-norm columns require an explicit documented treatment (e.g., remove nondiscriminating criterion and renormalize weights), never silent division by zero. TOPSIS is sensitive to normalization, cohort, and weights; expose these settings.

### 8.3 Pareto dominance

Strategy `A` dominates `B` if A is no worse on every included, direction-adjusted criterion and strictly better on at least one, within declared numerical tolerances. Produce nondominated fronts, not an arbitrary total order. Pareto analysis does not replace investor suitability screening.

### 8.4 Constraints and ties

Hard constraints are evaluated **before** rankings. A failing strategy is `ineligible`; insufficient evidence is `undetermined`. Neither may be presented as an eligible recommendation. Apply a documented tie tolerance and deterministic secondary ordering by stable strategy ID for display only; do not misrepresent ties as meaningful score differences.

## 9. Scenarios, robustness, and regret

Support three explicitly labeled scenario classes: historical replay, model-generated simulations, and hypothetical deterministic shocks. Historical references may include 2000–02, 2007–09, 2020, and 2022 only when valid sourced series are available. Do not invent scenario returns or probabilities.

For each comparable scenario calculate applicable returns, drawdowns, goal outcomes, eligibility changes, and ranks. Report worst observed/modelled outcome within the evaluated scenario set, not an absolute future worst case. For a benefit outcome `u(i,s)`, define scenario regret:

`regret(i,s) = max_{eligible k} u(k,s) - u(i,s)`

Report maximum regret and, only when defensible scenario probabilities are supplied, probability-weighted expected regret. Ranking stability is the share of evaluated configurations where a strategy ranks first/top-k; label this a **configuration frequency**, not a probability of future success, unless a justified probability model exists.

Sensitivity may vary criterion weights, risk assumptions, investor goals, and scenarios. Record perturbation ranges, grids/seeds, and number of evaluated configurations. Distinguish sampling uncertainty, parameter uncertainty, and structural model uncertainty.

## 10. Investor goals and suitability screening

Support multiple dated goals, target amounts, minimum success probabilities when supplied, contributions, withdrawals, liquidity, inflation assumptions, retirement longevity, and external income flows. Distinguish risk tolerance (willingness) from risk capacity (financial ability). Evaluate hard constraints separately from preferences. A strategy that violates a mandatory constraint is excluded even if it has the highest numerical score.

Use Skill #8 goal probabilities and depletion estimates only when compatible, validated simulation outputs exist. If essential profile or goal information is missing, report `undetermined` for investor-specific suitability. A generic strategy ranking may still be produced if independent comparison inputs are sufficient; it must not be described as personalized suitability advice.

## 11. Validation severity and fail-closed behavior

- **Critical:** invalid schemas; incompatible evaluation bases; nonfinite required metrics; impossible dates; missing hard-constraint inputs; invalid weights; unsupported model outputs; absent required provenance; material look-ahead evidence. Block affected calculation, ranking, or personalized assessment.
- **Warning:** short samples, fragile ranking, permitted reconciliations, uncertain estimates, high sensitivity, backtest bias risks, stale-but-usable data. Continue only with visible qualifications.
- **Information:** source timestamps, seeds, model versions, benchmark definitions, transformations, zero-discrimination criteria, and nonmaterial observations.

Validate data ranges, duplicate IDs, matrix dimensions where relevant, finite values, chronology, units, return conventions, schema versions, risk-free rate basis, and portfolio weights when provided. Reject malicious/unbounded payloads and path traversal. Preserve structured error codes, field paths, and severity. Never silently repair material data problems or fabricate precision.

## 12. Reproducibility, provenance, and security

Record source IDs, as-of timestamps, file checksums, calculation version, input contract version, scenario definitions, scoring weights, tie tolerances, normalization choices, random seeds, dependency versions, and transformation logs. Hash canonicalized inputs and outputs; do not put sensitive investor information in public logs or exported example fixtures. Enforce upload size limits, content validation, safe parsing, output escaping, request timeouts, and bounded computation. Do not execute uploaded Python, spreadsheet formulas, or arbitrary scripts. API keys remain server-side and optional.

## 13. Visualization and report contract

Dashboard: comparison overview, eligibility/comparability banners, score and rank tables, criterion contribution breakdown, risk-return scatterplot, historical growth/drawdown views, scenario heatmaps, goal probability views when supported, robustness and regret views, and assumptions/audit inspector. Do not show unavailable charts as if calculated.

Exports: `result.json` (versioned full precision), `metrics.csv` (tidy units/periods), `comparison.html` (self-contained where feasible), and `comparison.pdf` (readable report). All exports carry request ID, as-of date, model version, sources, material warnings, and educational-use disclaimer. Accessibility: keyboard navigation, readable contrast, text alternatives to visualizations, and tabular equivalents.

## 14. Architecture and interfaces

- **Python core:** pure deterministic functions for validation, reconciliation, screening, normalization, MCDA, scenarios, and reporting data.
- **API:** versioned request/result models; proposed endpoints `POST /api/v1/strategy-comparisons` and `GET /api/v1/strategy-comparisons/{request_id}`. Endpoint definitions are design targets, not claims of implementation.
- **Frontend:** HTML/CSS/JavaScript, responsive dashboard and input controls. Changes to weights must trigger deterministic recalculation; show source/assumption changes.
- **Agent adapters:** explicitly versioned input adapters for Skills #1–#8; optional natural-language interpreter reads immutable validated results. If unavailable, the mathematical workflow still functions.
- **Downstream:** Skill #10 receives validated rankings, eligibility, comparison classification, uncertainty, explanations, and evidence references. A blocked result cannot be promoted to a recommendation.

## 15. Six acceptance gates

### Gate 1 — Specifications and contracts

Deliver `SKILL.md`, README, versioned request/result JSON Schemas, valid/invalid examples, metric dictionary, provenance conventions, and schema/semantic contract tests. Acceptance: fixtures validate as expected and incompatibilities are explicitly classified.

### Gate 2 — Mathematical engine

Implement weighted normalization, weighted scoring, TOPSIS, Pareto fronts, tie handling, hard-constraint screening, and independently computed reference cases. Acceptance: deterministic repeatability, known numerical outputs, and full unit-test pass.

### Gate 3 — Scenario and suitability analytics

Implement scenario comparisons, sensitivity, robustness, regret, goal-based assessment, and missing-data safeguards. Acceptance: scenario labeling is correct; unsupported probabilities are absent; hard constraints block; uncertainty and ranking instability are disclosed.

### Gate 4 — Multi-agent integration

Verify adapters for available upstream skill versions, standalone import, Skill #10 output contract, optional AI explanations, and missing-service behavior. Acceptance: same validated inputs yield the same core numerical result with AI on or off.

### Gate 5 — Performance, security, and reliability

Test bounded realistic workloads, malformed/malicious inputs, file size and time limits, dependency failure, concurrency, exports, and regression. Acceptance: no silent failures, unauthorized execution, key leakage, or material nonreproducibility.

### Gate 6 — Hosted professor acceptance

Provide a reachable browser URL, no-login demo or supplied test credentials, sample portfolios, editable weights and assumptions, downloadable results, documentation, and a professor checklist. Acceptance: professor can run and verify deterministic comparisons **without providing an OpenAI API key**. Optional AI features are clearly separated and nonblocking.

No gate is considered passed merely because this specification exists. Capture evidence of execution and acceptance separately.

## 16. Minimum acceptance test inventory

1. Valid two-strategy request accepted; missing required fields rejected.
2. Duplicate strategy IDs and ambiguous percentage units rejected.
3. Incompatible historical periods prevent unqualified direct rankings.
4. Annualization, fees, currency, and benchmark mismatches detected and documented.
5. Weighted score matches hand-calculated reference and weights sum to one.
6. Cost/benefit directions and constant criteria handled correctly.
7. TOPSIS closeness, zero-norm, and tie behavior verified.
8. Pareto nondominance matches independent reference cases.
9. Ineligible and undetermined strategies excluded from eligible ranking.
10. Missing Monte Carlo goal inputs never produce invented probabilities.
11. Historical, modeled, and hypothetical scenarios remain clearly distinguished.
12. Regret and ranking stability reproduce from fixed inputs.
13. Different investor weights change rankings where reference case predicts.
14. JSON/CSV/HTML/PDF exports preserve units, sources, and warnings.
15. Cross-skill version mismatch fails closed with actionable errors.
16. Core result is identical with optional AI disabled.
17. Invalid uploads and resource limits fail safely.
18. Hosted professor can reproduce published reference outcomes without an API key.

## 17. Recommended repository structure

```text
strategy-comparison/
├── SKILL.md
├── README.md
├── pyproject.toml
├── schemas/
│   ├── strategy_comparison.request.v1.schema.json
│   └── strategy_comparison.result.v1.schema.json
├── examples/
│   ├── valid-baseline-request.json
│   ├── invalid-comparability-request.json
│   └── deterministic-reference-case.json
├── src/strategy_comparison/
│   ├── __init__.py
│   ├── contracts.py
│   ├── validation.py
│   ├── comparability.py
│   ├── suitability.py
│   ├── scoring.py
│   ├── topsis.py
│   ├── pareto.py
│   ├── scenarios.py
│   ├── robustness.py
│   ├── reporting.py
│   └── orchestrator.py
├── tests/
│   ├── test_contracts.py
│   ├── test_comparability.py
│   ├── test_scoring.py
│   ├── test_suitability.py
│   ├── test_scenarios.py
│   └── test_integration.py
└── .github/workflows/validate.yml
```

The tree is a **planned structure**, not a claim that these files exist. Develop and commit one reviewed file at a time. Defer detailed implementation and test expansion until the ten skill specifications are complete, while retaining each gate's required acceptance criteria.

## 18. Definition of done

Skill #9 is complete only when all six gates have documented passing evidence; all core algorithms are reproducible and independently tested; comparison and eligibility rules fail closed; report exports are traceable; Skill #10 consumes the versioned output; and the professor can execute the hosted deterministic workflow without supplying an OpenAI API key.
