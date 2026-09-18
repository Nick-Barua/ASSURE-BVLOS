# ASSURE-BVLOS

<p align="center">

![Status](https://img.shields.io/badge/status-revised%20reference%20implementation-0A66C2)
![Version](https://img.shields.io/badge/version-2.0.0--revision-1F6FEB)
![Python](https://img.shields.io/badge/python-3.13.5-3776AB?logo=python&logoColor=white)
![License](https://img.shields.io/badge/license-BSD--3--Clause-2EA44F)
![Evaluation](https://img.shields.io/badge/evaluation-100%2C000%20synthetic%20states-F59E0B)
![Tests](https://img.shields.io/badge/tests-28%20passed-2EA44F)
![Output Checks](https://img.shields.io/badge/output%20checks-65%20passed-2EA44F)
![Reproducibility](https://img.shields.io/badge/reproducibility-code%20%2B%20data%20%2B%20tests-00A67E)
![Journal](https://img.shields.io/badge/journal-Aerospace%20Systems-6F42C1)
![Zenodo](https://img.shields.io/badge/Zenodo-v1.0.0-1682D4?logo=zenodo&logoColor=white)

</p>

## Runtime Assurance for Risk-Adaptive Space–Air–Ground Unmanned Aircraft Operations

ASSURE-BVLOS is a research framework for supervisory runtime assurance in beyond-visual-line-of-sight unmanned aircraft operations. It integrates five monitored risk domains, explicit uncertainty penalties, deterministic hard-limit overrides, adaptive supervisory authority states and configuration-linked evidence.

**Author:** Nick Barua  
**Reference implementation:** `2.0.0-revision`  
**Associated manuscript:** *ASSURE-BVLOS: Runtime Assurance for Risk-Adaptive Space–Air–Ground Unmanned Aircraft Operations*  
**Journal submission:** *Aerospace Systems*

[Download Code, Data and Figures](ESM_1.zip) ·
[Supplementary Methods](ESM_2.pdf) ·
[Reproduce the Evaluation](#reproduce-the-evaluation)

> **Research scope:** This repository demonstrates the behaviour of a configured supervisory model under synthetic assumptions. It is not an aircraft controller, an operationally validated safety system, evidence of real-world accident reduction or evidence of certification compliance.

---

## Graphical Abstract

<p align="center">
  <img src="ASSURE_BVLOS%20GA.png"
       alt="ASSURE-BVLOS graphical abstract showing five monitored risk domains, uncertainty-adjusted supervisory logic, hard-limit overrides, five authority states and the revised computational evaluation."
       width="100%">
</p>

<p align="center">
  <em>
    Five monitored risk domains → uncertainty-aware risk fusion → deterministic hard-limit precedence → supervisory authority adaptation → configuration-linked evidence.
  </em>
</p>

> **Interpretation:** The graphical abstract summarises the revised reference implementation and replacement computational evaluation. The reported numerical results describe the behaviour of the configured synthetic model and should not be interpreted as validated flight-safety effectiveness, causal accident reduction or certification evidence.

---

## Project Status

| Category | Current status |
|---|---|
| **Framework maturity** | Reference supervisory runtime-assurance framework |
| **Analytical verification** | Completed within stated mathematical scope |
| **Executable implementation** | Available |
| **Primary computational evaluation** | Completed |
| **Primary sample size** | 100,000 independent synthetic states |
| **Repeated-seed evaluation** | 10 × 100,000-state runs completed |
| **Threshold sensitivity** | Completed |
| **Weight sensitivity** | Completed |
| **Domain ablation** | Completed |
| **Uncertainty-penalty sensitivity** | Completed |
| **Surrogate sensitivity** | Completed |
| **Mitigation sensitivity** | Completed |
| **Alternative fusion comparison** | Completed |
| **Temporal supervisory-logic testing** | Completed on prescribed-score traces |
| **Hardware-in-the-loop validation** | Not performed |
| **Human-in-the-loop validation** | Not performed |
| **Controlled flight validation** | Not performed |
| **Operational BVLOS validation** | Not performed |
| **Certification status** | Research framework; no certification claim |

---

## Evidence Levels

ASSURE-BVLOS deliberately separates computational maturity from operational validation.

| Level | Evidence stage | Status |
|---|---|---|
| **V1** | Analytical properties and software verification checks | **Completed within stated scope** |
| **V2** | Software simulation and temporal supervisory-logic testing | **Partially completed** |
| **V3** | Hardware-in-the-loop and human-in-the-loop validation | **Not performed** |
| **V4** | Controlled flight testing | **Not performed** |
| **V5** | Limited authorised operational BVLOS trial | **Not performed** |
| **V6** | Continuous assurance and configuration-controlled operation | **Not performed** |

> Completion of one evidence level does not imply completion of subsequent operational, airworthiness or certification stages.

---

## Revision Provenance

This repository now contains a **newly implemented computational evaluation** of the documented ASSURE-BVLOS framework.

The executable implementation and state-level outputs underlying the earlier numerical evaluation were unavailable for verification. Previously unreported parameters were therefore explicitly specified for the revised reference implementation, and the revised manuscript replaces the earlier numerical findings.

**This is not a recovered or verified reproduction of the original simulation.**

No parameter in the replacement implementation was selected for the purpose of reproducing the earlier headline result.

The distinction between:

- settings retained from the submitted framework, and
- parameters newly specified for the replacement evaluation

is documented in [Supplementary Methods S2](ESM_2.pdf).

The complete computational configuration is supplied inside [Supplementary Code S1](ESM_1.zip).

The identifier:

```text
2.0.0-revision
```

identifies the supplied reference implementation. It does not itself indicate publication acceptance, certification, a new Zenodo DOI or a formal GitHub software release.

---

## Supplementary Materials

| Resource | Description |
|---|---|
| **[ESM_1.zip — Supplementary Code S1](ESM_1.zip)** | Executable Python implementation, configuration, complete 100,000-state primary population, paired outcomes, sensitivity analyses, numerical results, tests, audit outputs, equation sources and figure sources |
| **[ESM_2.pdf — Supplementary Methods S2](ESM_2.pdf)** | Parameter provenance, generator specification, normalisation rules, statistical methods, supplementary figures, sensitivity descriptions and reproduction guidance |

### Important

The executable implementation is contained inside:

```text
ESM_1.zip
```

It is not stored as a separate source-code directory at the repository root.

After extraction, the main directory is:

```text
Supplementary_Code_S1/
```

The package contains the **complete analysis population**.

The included 1,000-row CSV file is only a convenience sample and is **not** the population used to generate the reported results.

---

# Framework

## Five Monitored Risk Domains

ASSURE-BVLOS uses five normalised runtime risk domains.

| Code | Domain | Reference implementation inputs |
|---|---|---|
| `rC2` | **Command-and-control integrity** | Round-trip time, jitter and packet loss |
| `rS` | **Sensing reliability** | Sensor-confidence deficit |
| `rD` | **External-data integrity** | Provenance deficit |
| `rE` | **Environmental exposure** | Weather and exposure scores |
| `rV` | **Vehicle health** | Energy reserve and health deficit |

Two additional quantities are intentionally represented separately:

| Quantity | Meaning |
|---|---|
| `Ds` | Sensor disagreement |
| `Ad` | Bounded external-data-age term |

These uncertainty terms are kept separate from the corresponding nominal sensing and data scores in the reference implementation to avoid counting those specific observables twice.

---

## Nominal Risk Aggregation

The nominal engineering index is a weighted multiplicative complement:

```math
R = 1 - \prod_{i:w_i>0}\left(1-r_i\right)^{w_i}
```

subject to:

```math
\sum_i w_i = 1,
\qquad
w_i \geq 0
```

The revised reference configuration uses equal weights:

```math
w_{C2} = w_S = w_D = w_E = w_V = 0.20
```

Zero-weight domains are omitted from the product.

### Mathematical Interpretation

For the stated domain range and non-negative unit-sum weights, the nominal index is:

- bounded in `[0,1]`;
- monotonic in each active input;
- continuous over the closed input domain;
- smooth in the interior; and
- sensitive to concurrent degradation.

It remains an **engineering ranking index**.

It is **not** a calibrated probability of an aircraft accident.

Equal weights are used as an explicit uncalibrated reference configuration. They do not imply that the five physical domains have equal operational safety importance.

---

## Uncertainty-Adjusted Decision Score

The supervisory decision score is:

```math
R^{*} =
\min\left[
1,\;
R + \lambda_s D_s + \lambda_a A_d
\right]
```

with:

```math
\lambda_s = \lambda_a = 0.10
```

where:

- `D_s` represents sensor disagreement; and
- `A_d` represents the bounded external-data-age term.

Each uncertainty term can add at most `0.10` to the nominal risk index before clipping in the reference configuration.

The clipping operation ensures:

```math
0 \leq R^{*} \leq 1
```

The uncertainty penalties are explicit engineering assumptions rather than statistically calibrated confidence bounds.

Sensor disagreement and data age are represented separately from the five nominal domain scores so that these particular uncertainty indicators remain visible in the supervisory decision and are not counted twice in the reference implementation.

---

# Supervisory Authority

## Authority States

| State | Nominal interval | Supervisory request |
|---|---|---|
| **S0 — Nominal** | `R* < 0.25` | Continue under supervision within the declared envelope |
| **S1 — Constrained** | `0.25 ≤ R* < 0.42` | Constrain speed, separation or manoeuvres; increase monitoring |
| **S2 — Protected autonomy** | `0.42 ≤ R* < 0.60` | Request an on-board protected response while retaining supervision |
| **S3 — Contingency** | `0.60 ≤ R* < 0.76` | Request hold, diversion, return, landing or hazard-volume exit |
| **S4 — Minimum risk / terminate** | `R* ≥ 0.76` | Request the predefined minimum-risk or termination procedure |

These thresholds are **synthetic methodological reference settings**.

They are not:

- regulatory thresholds,
- operational approval limits,
- certified acceptance criteria,
- or demonstrated optimal thresholds.

---

## Deterministic Hard-Limit Overrides

Hard conditions can bypass the scalar fusion layer.

In the reference implementation, both comparison policies request **S4** for:

- declared flight-control failure; or
- critically low energy reserve.

They request at least **S3** for:

- invalid navigation;
- geofence breach;
- very high C2 risk.

The adaptive ASSURE-BVLOS policy additionally requests at least **S2** under the configured moderate-C2 protection condition.

The selected authority request is the most conservative state required by either:

1. the aggregate decision score; or
2. an applicable hard-limit rule.

These rules test supervisory precedence.

They do **not** demonstrate:

- independence of the operational monitor,
- physical fault recovery,
- feasibility of a commanded trajectory,
- successful landing,
- or successful termination.

---

# Computational Evaluation

## Study Design

The primary evaluation uses:

```text
100,000 independent synthetic states
```

Random-number generator:

```text
NumPy PCG64
```

Primary seed:

```text
20260918
```

The three equiprobable synthetic mission archetypes are:

1. **Disaster response**
2. **Linear-infrastructure inspection**
3. **Maritime surveillance**

These mission archetypes are controlled test scenarios.

They are **not** intended to represent the measured global distribution of BVLOS operations.

---

## Static Baseline

The reference baseline is a **static hard-limit supervisory comparator**.

It assumes ordinary lower-level flight stabilisation but does not:

- fuse the five risk domains;
- use the uncertainty-adjusted aggregate score; or
- respond to the configured moderate-C2 protection threshold.

It retains the shared hard-limit protections.

The baseline is therefore not an uncontrolled aircraft and is not presented as representative of every existing BVLOS architecture.

---

## Synthetic Outcome Surrogate

The framework is evaluated using a configured logistic hazardous-outcome surrogate.

The surrogate uses the same broad risk inputs monitored by the policy.

State-dependent nominal mitigation factors are:

| State | Assumed mitigation |
|---|---:|
| S0 | 0% |
| S1 | 22% |
| S2 | 43% |
| S3 | 66% |
| S4 | 82% |

These are synthetic scenario-test assumptions.

They are **not empirical measurements of intervention effectiveness**.

Because the adaptive policy can request more conservative states and mitigation increases with authority state, the nominal outcome model structurally favours the adaptive policy.

The primary result must therefore be interpreted as a **configured model comparison**, not independent evidence of causal safety improvement.

---

# Primary Results

## Paired Synthetic Comparison

| Measure | Result |
|---|---:|
| Baseline hazardous outcomes | **10,739 / 100,000** |
| ASSURE-BVLOS hazardous outcomes | **8,083 / 100,000** |
| Baseline realised simulated outcome proportion | **10.739%** |
| ASSURE-BVLOS realised simulated outcome proportion | **8.083%** |
| Absolute paired difference | **2.656 percentage points** |
| 95% paired-bootstrap interval | **2.557–2.757 percentage points** |
| Relative difference from baseline | **24.73%** |

The paired cells are:

| Pair outcome | States |
|---|---:|
| Neither policy produces an outcome | 89,261 |
| Baseline only | 2,656 |
| ASSURE-BVLOS only | 0 |
| Both policies | 8,083 |

The absence of ASSURE-BVLOS-only outcomes follows from the nominal nested probability construction and shared outcome draw.

It should **not** be interpreted as evidence that the system eliminates every possible real-world failure mode.

---

## Statistical Interpretation

The primary analysis uses one common uniform random variate per synthetic state for both comparison policies, preserving pairing.

The paired bootstrap uses:

```text
10,000 replicates
```

Bootstrap seed:

```text
20261001
```

The confidence interval quantifies Monte Carlo sampling variability **conditional on the specified synthetic generator and surrogate model**.

It does not quantify:

- model-form uncertainty,
- uncertainty in assumed mitigation effectiveness,
- uncertainty in operational exposure,
- real-world accident risk,
- or regulatory safety confidence.

### Important terminology

The reported percentages are:

> **dimensionless simulated hazardous-outcome proportions**

They are **not**:

- events per flight hour,
- events per mission,
- events per kilometre,
- operational accident rates,
- or certified safety probabilities.

---

# Mission-Specific Results

| Mission archetype | Baseline | ASSURE-BVLOS |
|---|---:|---:|
| **Disaster response** | 13.60% | 10.02% |
| **Linear-infrastructure inspection** | 7.43% | 5.89% |
| **Maritime surveillance** | 11.18% | 8.33% |

S2–S4 intervention burden:

| Mission archetype | S2–S4 burden |
|---|---:|
| Disaster response | **11.93%** |
| Linear-infrastructure inspection | **6.91%** |
| Maritime surveillance | **10.46%** |

The configured disaster scenario has lower confidence and greater environmental burden than the infrastructure case.

The configured maritime scenario combines older external information with lower energy reserve.

These differences arise from the specified synthetic generator.

They should not be interpreted as measured rankings of real-world mission safety.

---

# Authority-State Behaviour

Nominal ASSURE-BVLOS authority occupancy:

| State | Number of states | Share |
|---|---:|---:|
| **S0** | 24,446 | 24.446% |
| **S1** | 65,786 | 65.786% |
| **S2** | 8,951 | 8.951% |
| **S3** | 270 | 0.270% |
| **S4** | 547 | 0.547% |

Therefore:

```text
S2–S4 intervention burden = 9.768%
```

and:

```text
S1–S4 constrained/intervention share = 75.554%
```

The nominal configured critical-state recall is:

```text
40.20%
```

The corresponding compound-only recall is:

```text
36.36%
```

These labels are author-specified diagnostics derived from the synthetic monitored inputs.

They are not independently measured operational ground truth.

---

# Residual Simulated Outcomes

The revised analysis explicitly evaluates the 8,083 remaining ASSURE-BVLOS synthetic outcomes.

| Authority state | Residual outcomes |
|---|---:|
| S0 | 1,100 |
| S1 | 5,479 |
| S2 | 1,466 |
| S3 | 21 |
| S4 | 17 |

Approximately:

```text
81.39%
```

of residual synthetic outcomes occur in S0 or S1.

This occurs because:

- the surrogate remains non-zero below intervention thresholds;
- S1 provides only partial assumed mitigation;
- higher-risk states remain probabilistic;
- even S4 retains residual surrogate probability under the nominal mitigation schedule.

These are modelled outcomes, not diagnosed real aircraft failures.

---

# Sensitivity Analyses

The revised package includes:

- **25** threshold settings;
- **5** leave-one-domain-out ablations;
- **7** vehicle-weight settings;
- **200** random Dirichlet weight vectors;
- **25** uncertainty-penalty combinations;
- multiple surrogate coefficient/intercept configurations;
- interaction-term removal;
- **4** mitigation schedules;
- **4** adverse-intervention assumptions;
- arithmetic fusion comparison;
- multiplicative fusion comparison;
- maximum fusion comparison;
- **10** random seeds;
- prescribed-score temporal supervisor testing.

These are finite diagnostic sensitivity analyses.

They are not a full global variance decomposition or Sobol analysis.

---

## Threshold Sensitivity

| Threshold multiplier | Mean conditional ASSURE probability | Critical recall | S2–S4 burden |
|---|---:|---:|---:|
| **0.70** | 6.127% | 96.73% | 51.98% |
| **0.90** | 7.543% | 61.33% | 16.39% |
| **1.00** | 7.963% | 40.20% | 9.77% |
| **1.10** | 8.268% | 29.51% | 7.62% |
| **1.30** | 8.821% | 25.65% | 7.00% |

Lower thresholds increase critical-state recall but substantially increase supervisory intervention burden.

No threshold configuration is declared optimal.

---

## Domain Ablation

With equal nominal weights:

- omitting vehicle health reduced recall from **40.20% to 36.65%**;
- omitting environment reduced recall to **32.37%**;
- omitting sensing produced **38.36%** recall;
- omitting data produced **39.64%** recall;
- omitting C2 increased recall to **51.82%** under the retained independent C2 protection channel and renormalised remaining weights.

These results demonstrate interactions between:

- aggregate weights,
- renormalisation,
- uncertainty penalties,
- and independent hard-limit/protection rules.

An apparent improvement under ablation does not imply that the omitted domain should be removed operationally.

---

## Vehicle-Weight Sensitivity

Nominal vehicle-health weight:

```text
0.20
```

At vehicle-health weight `0.40`:

```text
Critical-state recall = 51.36%
S2–S4 burden = 13.42%
```

At vehicle-health weight `0.60`:

```text
Critical-state recall = 63.83%
S2–S4 burden = 20.71%
```

Because the weights sum to one, increasing one domain necessarily decreases the aggregate weighting available to the other domains.

Therefore, these results do not show a free increase in safety sensitivity.

---

# Fusion Alternatives

At the nominal thresholds:

| Fusion method | Critical recall | S2–S4 burden |
|---|---:|---:|
| Arithmetic | 33.35% | 8.27% |
| Multiplicative | 40.20% | 9.77% |
| Maximum | 93.70% | 65.80% |

Because each rule produces a different intervention burden, an additional matched-budget comparison was performed.

At a budget of:

```text
9,768 interventions
```

the corresponding critical-state recalls were:

| Fusion method | Matched-budget recall |
|---|---:|
| **Arithmetic** | **40.45%** |
| **Multiplicative** | **40.20%** |
| **Maximum** | **31.78%** |

Therefore, the study does **not** claim that multiplicative fusion is empirically superior.

Its purpose in ASSURE-BVLOS is to provide a transparent, bounded and analytically tractable reference aggregation rule.

---

# Intervention-Assumption Sensitivity

When all mitigation is set to zero:

```text
Baseline outcomes = ASSURE-BVLOS outcomes
```

Under a separate adverse-intervention stress test, an additional conditional probability is added wherever ASSURE-BVLOS requests greater authority than the baseline.

At:

```text
k = 0.05
```

the mean conditional difference becomes:

```text
−1.1055 percentage points
```

meaning the direction of the nominal comparison reverses.

The approximate break-even value is:

```text
k ≈ 0.03522
```

per additionally assigned state.

This stress test demonstrates that the headline comparison depends materially on assumptions about intervention effectiveness.

The adverse-intervention values are methodological stress parameters.

They are not empirical estimates of intervention risk.

---

# Repeated-Seed Stability

Ten independent seeds were evaluated.

Each seed generated:

```text
100,000 states
```

The realised paired difference ranged from:

```text
2.534 to 2.711 percentage points
```

Mean:

```text
2.6201 percentage points
```

Sample standard deviation:

```text
0.0513 percentage points
```

The mean conditional probability difference was:

```text
2.6286 percentage points
```

with standard deviation:

```text
0.0128 percentage points
```

These results assess Monte Carlo stability under the same configured generator.

They do not establish robustness to unmodelled aircraft, network, environmental or human mechanisms.

---

# Temporal Supervisory-Logic Test

The primary Monte Carlo evaluation samples independent states and does **not** simulate a temporal flight mission.

A separate supervisory-logic test uses:

```text
500 traces
600 ticks per trace
1 second per tick
300,000 total evaluated ticks
```

The same prescribed-score traces are supplied to:

1. a memoryless supervisor; and
2. a hysteretic supervisor.

Results:

| Metric | Memoryless | Hysteretic |
|---|---:|---:|
| Mean state changes per trace | **97.556** | **5.836** |
| Mean S2–S4 occupancy | **29.699%** | **63.727%** |

The hysteretic logic strongly reduces switching but substantially increases protected-state occupancy.

No configured override-floor violation occurred in the 300,000 tested ticks.

These tests concern **supervisory requests only**.

They do not demonstrate:

- aircraft dynamic stability,
- safe closed-loop switching,
- bounded physical transition latency,
- acceptable pilot workload,
- realistic network behaviour,
- successful fallback execution,
- or mission-level safety improvement.

---

# Reproduce the Evaluation

## 1. Download

Download:

[**ESM_1.zip**](ESM_1.zip)

---

## 2. Extract

From the directory containing the downloaded archive:

```bash
python -m zipfile -e ESM_1.zip .
cd Supplementary_Code_S1
```

Keep the original downloaded archive unchanged as the reference copy.

---

## 3. Create a Python Environment

The supplied execution environment used:

```text
Python 3.13.5
```

Create a virtual environment:

```bash
python -m venv .venv
```

### Linux / macOS

```bash
source .venv/bin/activate
```

### Windows PowerShell

```powershell
.\.venv\Scripts\Activate.ps1
```

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

The recorded principal versions are:

```text
numpy==2.3.5
scipy==1.17.0
pandas==2.2.3
matplotlib==3.10.8
```

Exact replay was verified in the recorded environment.

A fresh installation on a different platform was not independently tested for byte-level equality.

---

## 4. Run the Tests

```bash
python -m unittest discover -s tests -v
```

Recorded result:

```text
28 tests passed
```

---

## 5. Reproduce the Computational Study

```bash
python run_study.py --config config.json --output replay
```

The `replay` directory keeps regenerated outputs separate from the supplied reference results.

---

## 6. Verify Outputs

```bash
python verify_outputs.py --results replay
```

The verification procedure checks:

- primary-array consistency;
- stored hashes;
- headline outcome totals;
- scenario totals;
- derived numerical outputs.

---

## 7. Regenerate Numerical Figures

```bash
python make_figures.py --results replay --output replay_figures
```

This regenerates the numerical figures associated with:

- Figure 4;
- Figure 5;
- Figure 6;
- Figure 7;
- Figure S1;
- Figure S2;
- Figure S3.

Figures 1–3 are conceptual artwork and have separate source/rendering files.

After Python dependencies are installed, reproducing the numerical study requires:

- no external dataset;
- no web API;
- no model download;
- no GPU;
- no operational aircraft system.

---

# Package Structure

```text
Supplementary_Code_S1/
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
├── config.json
├── CONFIG_SHA256.txt
├── MANIFEST_SHA256.txt
├── data_dictionary.csv
├── equations.tex
├── reference_list.json
├── run_study.py
├── verify_outputs.py
├── make_figures.py
├── draw_conceptual_figures.py
│
├── assure_bvlos/
│   ├── __init__.py
│   └── model.py
│
├── tests/
│   └── test_model.py
│
├── results/
│   ├── primary_states.npz
│   ├── primary_states_sample_1000.csv
│   ├── summary.json
│   ├── scenario_results.csv
│   ├── residual_by_authority.csv
│   ├── threshold_sweep.csv
│   ├── domain_ablation.csv
│   ├── vehicle_weight_sweep.csv
│   ├── random_weight_sensitivity.csv
│   ├── uncertainty_penalty_sweep.csv
│   ├── aggregation_comparison.csv
│   ├── matched_budget_comparison.csv
│   ├── surrogate_sensitivity.csv
│   ├── mitigation_sensitivity.csv
│   ├── intervention_risk_sensitivity.csv
│   ├── seed_results.csv
│   ├── temporal_logic_results.csv
│   ├── temporal_example.csv
│   ├── primary_array_sha256.json
│   ├── replay_checks.json
│   ├── verification_report.json
│   ├── unit_tests.txt
│   └── run_log.txt
│
├── figures/
└── audit/
```

---

# Data Files

## `primary_states.npz`

Contains the complete primary synthetic population.

The stored information includes:

- generated observables;
- five normalised risk domains;
- uncertainty terms;
- scenario identifiers;
- link identifiers;
- hard-fault flags;
- aggregate decision score;
- baseline authority state;
- ASSURE-BVLOS authority state;
- conditional probabilities;
- common random outcome variate;
- paired binary outcomes;
- critical-state labels.

---

## `primary_states_sample_1000.csv`

This is a convenience sample for inspection.

It is **not** the analysis population.

---

## `data_dictionary.csv`

Documents the arrays and variables included in the supplied data.

---

# Recorded Verification

The final packaged audit records:

| Check | Result |
|---|---:|
| Implementation tests | **28 passed** |
| Output-consistency checks | **65 passed** |
| Primary arrays matching same-environment replay | **29** |
| Result CSV files matching byte-for-byte | **16** |

The recorded configuration SHA-256 is:

```text
82ab6791a44b771a66360d719d94c7ca6a800cd127e0f7e7721d3148fa256baa
```

A SHA-256 value identifies exact file contents.

It does **not** prove:

- scientific correctness;
- preregistration;
- software qualification;
- aviation certification;
- external validation;
- or operational safety.

---

# Figures

The revised figure set is contained inside `ESM_1.zip`.

| Figure | Description |
|---|---|
| **Figure 1** | Proposed ASSURE-BVLOS architecture |
| **Figure 2** | Authority bands and proposed recovery logic |
| **Figure 3** | Illustrative safety-case claims and evidence |
| **Figure 4** | Primary normalised domain distributions |
| **Figure 5** | Primary paired simulated outcome probabilities |
| **Figure 6** | Simulated outcome probabilities by mission archetype |
| **Figure 7** | Threshold sensitivity of recall and intervention burden |
| **Figure S1** | Vehicle weighting and recall–burden trade-off |
| **Figure S2** | Sensitivity to an adverse-intervention assumption |
| **Figure S3** | Example prescribed-score authority trace |

Figures S1–S3 are also included in:

[**ESM_2.pdf**](ESM_2.pdf)

---

# Historical Artwork Notice

The older standalone manuscript figures belong to the earlier numerical evaluation.

In particular, artwork showing approximately:

```text
Baseline:       12.758%
ASSURE-BVLOS:    9.619%
```

does **not** represent the replacement evaluation.

The current reference results are:

```text
Baseline:       10.739%
ASSURE-BVLOS:    8.083%
```

Use the revised figures supplied inside `ESM_1.zip` for the current computational evaluation.

Older artwork may be retained for historical provenance but should not be presented as current evidence.

---

# Validation Roadmap

## V1 — Analytical and Software Checks

**Status:** Completed within stated scope

Current evidence includes:

- boundedness analysis;
- monotonicity analysis;
- continuity discussion;
- endpoint handling;
- hard-limit precedence testing;
- 28 implementation tests;
- exact reference replay checks.

This does not establish aircraft safety.

---

## V2 — Software Simulation

**Status:** Partial

Completed:

- independent-state synthetic evaluation;
- paired outcome analysis;
- threshold sensitivity;
- weight sensitivity;
- ablation analysis;
- surrogate sensitivity;
- mitigation sensitivity;
- repeated seeds;
- prescribed-score temporal supervisor testing.

Still required:

- realistic temporal missions;
- correlated network behaviour;
- coupled aircraft dynamics;
- encounter geometry;
- common-cause faults;
- independently specified physical outcomes.

---

## V3 — Hardware-in-the-Loop / Human-in-the-Loop

**Status:** Not performed

Future evidence should include:

- autopilot integration;
- communications emulator;
- monitor timing;
- fault injection;
- operator station;
- annunciation;
- mode awareness;
- pilot workload;
- intervention timing.

---

## V4 — Controlled Flight

**Status:** Not performed

Future work requires an approved test environment and predefined scenario matrix with independent observation of actual aircraft response.

---

## V5 — Limited Operational Trial

**Status:** Not performed

A later authorised BVLOS trial would require:

- missions or flight-hour denominators;
- operational exposure information;
- workload evidence;
- monitor performance;
- residual-risk evidence;
- operational acceptance criteria.

---

## V6 — Continuous Assurance

**Status:** Not performed

Future operational deployment would require:

- configuration-controlled updates;
- regression testing;
- change-impact assessment;
- traceable evidence;
- independent review.

---

# Current Scientific Limitations

The reference evaluation uses:

- synthetic independent states;
- engineering normalisations;
- newly specified generator parameters;
- assumed surrogate coefficients;
- prescribed mitigation factors;
- synthetic fault flags;
- author-specified diagnostic labels.

The main computational evaluation does not model:

- aircraft dynamics;
- physical encounter geometry;
- realistic temporal network behaviour;
- additional measurement uncertainty;
- detailed detect-and-avoid performance;
- pilot adaptation;
- mission completion;
- progressive cyberattack;
- common-cause failures.

The outcome surrogate and policy also share monitored inputs.

Consequently, the nominal paired result is **not an independently identified treatment effect**.

---

# Responsible Use

ASSURE-BVLOS is a research and reproducibility implementation.

It must not be treated as a certified or operational flight-safety system.

The current evidence does not establish:

- real-world accident probability;
- causal safety effectiveness;
- verified contingency feasibility;
- verified termination effectiveness;
- acceptable pilot workload;
- safe closed-loop aircraft behaviour;
- regulatory compliance;
- certification credit.

> **Do not use this research implementation as the sole basis for real-world flight-safety, termination, certification or deployment decisions.**

---

# Citation

For work using the replacement computational implementation, record the exact Git commit and implementation identifier used.

Suggested citation:

> **Barua, N. (2026). ASSURE-BVLOS: New Reference Computational Evaluation. Reference implementation 2.0.0-revision. GitHub.**

Repository:

```text
https://github.com/Nick-Barua/ASSURE-BVLOS
```

---

## Earlier Zenodo Record

The earlier Version 1.0.0 supporting-materials snapshot is archived at:

**DOI:** [10.5281/zenodo.21642250](https://doi.org/10.5281/zenodo.21642250)

That Version 1.0.0 record contains earlier documentation and figures.

It does **not** contain the executable implementation underlying the revised reference evaluation.

Do not cite the earlier DOI as though it contains the replacement `2.0.0-revision` computation.

A later archival version should preserve the provenance distinction between:

1. the earlier Version 1.0.0 documentation snapshot; and
2. the replacement executable reference evaluation.

---

# Reproducibility Reporting

When reporting a reproducibility problem, include:

- repository commit;
- implementation version;
- operating system;
- Python version;
- NumPy version;
- SciPy version;
- pandas version;
- command executed;
- configuration changes;
- complete error output.

Do not include:

- confidential operational information;
- authentication credentials;
- personal information;
- proprietary mission data.

---

# AI-Assisted Development Disclosure

Generative AI assistance was used during the revised study for:

- drafting;
- mathematical exposition;
- reference-code generation;
- test construction;
- presentation support.

The supplied test outputs and replay records document computational checks that were actually executed.

AI-generated material is not treated as independent scientific evidence.

Responsibility for:

- scientific interpretation;
- assumptions;
- software use;
- results;
- manuscript content;
- and final submission

remains with the author.

---

# Licence

This repository and the reference implementation use the:

## BSD 3-Clause License

See:

[LICENSE](LICENSE)

and the licence included in `ESM_1.zip`.

The software is supplied without warranty of operational suitability, airworthiness, safety or fitness for a particular aviation purpose.

---

# Contact

**Nick Barua**

ORCID:  
[0000-0003-4641-0112](https://orcid.org/0000-0003-4641-0112)

Email:  
[s.nick.barua@gmail.com](mailto:s.nick.barua@gmail.com)

Repository:  
https://github.com/Nick-Barua/ASSURE-BVLOS

Technical and reproducibility issues:  
[GitHub Issues](https://github.com/Nick-Barua/ASSURE-BVLOS/issues)

---

<p align="center">
  <strong>ASSURE-BVLOS</strong><br>
  Runtime assurance for risk-adaptive space–air–ground unmanned aircraft operations
</p>

<p align="center">
  <em>
    Reproducible supervisory-model behaviour • explicit uncertainty handling • deterministic hard-limit precedence • traceable evidence
  </em>
</p>
