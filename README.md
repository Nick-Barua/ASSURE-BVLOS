# ASSURE-BVLOS

## Runtime Assurance for Risk-Adaptive Space–Air–Ground Unmanned Aircraft Operations

A research framework linking five monitored risk domains, explicit uncertainty penalties, supervisory authority states, deterministic hard-limit overrides and configuration-linked evidence.

**Author:** Nick Barua  
**Reference implementation:** `2.0.0-revision`  
**Associated manuscript:** *ASSURE-BVLOS: Runtime Assurance for Risk-Adaptive Space–Air–Ground Unmanned Aircraft Operations*  
**Journal submission:** Aerospace Systems

[Download code, data and figures](ESM_1.zip) · [Read supplementary methods](ESM_2.pdf) · [Reproduce the evaluation](#reproduce-the-evaluation)

> **Research scope:** This package demonstrates the behaviour of a configured supervisory model under synthetic assumptions. It is not an aircraft controller, an operationally validated safety system or evidence of certification compliance.

---

## Revision provenance

This repository's revised computational materials contain a **newly implemented evaluation** of the documented ASSURE-BVLOS framework.

The executable implementation and state-level outputs underlying the earlier numerical evaluation were unavailable for verification. Previously unreported parameters have therefore been explicitly specified for the new reference implementation, and the revised manuscript replaces the earlier numerical findings.

**This is not a recovered or verified reproduction of the original simulation.** No parameter was selected to reproduce the earlier headline result.

The distinction between retained methodological settings and newly specified assumptions is documented in [Supplementary Methods S2](ESM_2.pdf). The complete numerical configuration is supplied inside [Supplementary Code S1](ESM_1.zip).

The identifier `2.0.0-revision` identifies the supplied implementation. It does not, by itself, indicate that a corresponding GitHub release or new Zenodo record has been published.

---

## Current supplementary materials

| File | Contents |
|---|---|
| **[ESM_1.zip — Supplementary Code S1](ESM_1.zip)** | Executable Python implementation, configuration, all 100,000 primary synthetic states and paired outcomes, numerical results, sensitivity analyses, tests, verification logs, equation sources and revised figures. |
| **[ESM_2.pdf — Supplementary Methods S2](ESM_2.pdf)** | Parameter provenance, complete generator settings, normalisation and authority algorithms, statistical methods, supplementary figures and reproduction instructions. |

**The executable code is inside `ESM_1.zip`, not in a separate source-code directory at the repository root.** Extract the archive before running the commands below.

The archive extracts to a folder named:

```text
Supplementary_Code_S1/
```

The package includes the complete primary population. The accompanying 1,000-row CSV is a convenience excerpt, not the dataset used for the reported analysis.

<!--
GRAPHICAL ABSTRACT — ACTIVATE AFTER CORRECTION

Before displaying the revised graphical abstract:
1. Replace "Event rate (%)" with "Simulated hazardous-outcome probability (%)".
2. Route sensor disagreement and data age into the adjusted decision-score block.
3. Upload the corrected image to the repository root using this exact filename:
   ASSURE_BVLOS_Graphical_Abstract_Revised.png

Then remove this enclosing HTML comment.

## Graphical abstract

![ASSURE-BVLOS: five monitored domains, uncertainty-adjusted supervisory logic, hard-limit precedence and replacement synthetic evaluation](ASSURE_BVLOS_Graphical_Abstract_Revised.png)

The graphical abstract is a visual summary. The executable configuration and supplementary methods define the model, numerical assumptions and interpretation.

---
-->

## Framework overview

ASSURE-BVLOS combines five normalised domain scores.

| Domain | Reference implementation inputs |
|---|---|
| **Command-and-control integrity** | Round-trip time, jitter and packet loss. |
| **Sensing reliability** | Sensor-confidence deficit. |
| **External-data integrity** | Provenance deficit. |
| **Environmental exposure** | Weather and exposure scores. |
| **Vehicle health** | Energy reserve and health deficit. |

Sensor disagreement and bounded external-data age enter as **separate uncertainty penalties**. They are excluded from the corresponding nominal sensing and data scores in this implementation to avoid counting those particular inputs twice.

### Risk aggregation

The nominal engineering index is:

$$
R = 1 - \prod_{i:w_i>0}(1-r_i)^{w_i},
\qquad
\sum_i w_i = 1,
\qquad
w_i \geq 0.
$$

All five reference weights are `0.20`. Zero-weight domains are omitted from the product.

The uncertainty-adjusted decision score is:

$$
R^{*}
=
\min\left(1,\;R+\lambda_s D_s+\lambda_a A_d\right),
\qquad
\lambda_s=\lambda_a=0.10.
$$

Here, `D_s` is sensor disagreement and `A_d` is the bounded data-age term.

The nominal index is bounded and monotonic for the stated inputs and weights. **It is not a calibrated accident probability.** Equal weighting is an uncalibrated reference choice, not evidence that all domains have equal physical safety significance.

### Supervisory authority states

| State | Nominal score interval | Requested response |
|---|---|---|
| **S0 — Nominal** | `R* < 0.25` | Continue under supervision within the declared envelope. |
| **S1 — Constrained** | `0.25 ≤ R* < 0.42` | Constrain operation or increase monitoring. |
| **S2 — Protected autonomy** | `0.42 ≤ R* < 0.60` | Request an on-board protected response. |
| **S3 — Contingency** | `0.60 ≤ R* < 0.76` | Request a hold, diversion, return, landing or hazard-volume exit. |
| **S4 — Minimum risk / terminate** | `R* ≥ 0.76` | Request the predefined minimum-risk or termination procedure. |

These thresholds are **synthetic reference settings**, not operational acceptance criteria or regulatory limits.

Deterministic hard-limit rules bypass scalar aggregation and enforce minimum requested states. Both policies share S3/S4 hard-limit protections; the adaptive policy additionally includes a moderate-C2 protection floor.

The static baseline assumes ordinary lower-level flight stabilisation but does not aggregate cross-domain evidence. It is a defined reference comparator, not a representation of every existing BVLOS architecture.

Logical precedence does not establish physical independence of an operational safety monitor. Likewise, requesting a response does not prove that an aircraft can execute it safely.

---

## Primary computational results

The primary evaluation uses **100,000 independent synthetic states**, NumPy's PCG64 generator and seed `20260918`.

The three archetypes are disaster response, linear-infrastructure inspection and maritime surveillance. These are controlled synthetic scenarios, not measured samples of operational missions.

| Measure | Revised reference result |
|---|---:|
| Baseline simulated hazardous outcomes | 10,739 / 100,000 |
| ASSURE-BVLOS simulated hazardous outcomes | 8,083 / 100,000 |
| Baseline realised outcome proportion | **10.739%** |
| ASSURE-BVLOS realised outcome proportion | **8.083%** |
| Paired difference, baseline minus ASSURE-BVLOS | **2.656 percentage points** |
| 95% paired-bootstrap interval for the difference | **2.557–2.757 percentage points** |
| Relative difference from the baseline | 24.73% |
| Critical-state recall | 40.20% |
| S2–S4 intervention burden | 9.768% |
| S1–S4 combined constraint/intervention share | 75.554% |

The bootstrap uses 10,000 paired resampling replicates. Its interval describes Monte Carlo sampling variability conditional on the configured model. It does not quantify uncertainty about real-world safety effectiveness.

**These percentages are dimensionless simulated outcome proportions—not hazardous events per flight hour, per mission or per kilometre.**

### Interpretation of the comparison

The baseline and adaptive policies receive the same generated states and share the outcome random variates.

The nominal mitigation schedule increases with the requested authority state. Because the adaptive policy requests states at least as conservative as the baseline, these assumptions structurally favour its nominal outcome comparison.

**The lower simulated proportion is therefore not independent evidence that ASSURE-BVLOS reduces real-world accidents.** The evaluation examines allocation, magnitude, sensitivity and internal consistency within the specified model.

The current numerical sources are `results/summary.json` and the generated CSV files inside `ESM_1.zip`.

---

## Sensitivity analyses and contrary findings

The package includes threshold sensitivity, domain ablation, vehicle-weight variation, random weight perturbations, uncertainty-penalty variation, surrogate and mitigation sensitivity, repeated random seeds, and alternative aggregation comparisons.

Important qualifications are retained:

- **Threshold trade-offs:** Lower thresholds increase critical-state recall but also increase intervention burden.
- **Fusion comparison:** At the matched budget of 9,768 interventions, arithmetic fusion achieved 40.45% recall, compared with 40.20% for multiplicative fusion and 31.78% for maximum fusion. This retrospective diagnostic does not establish multiplicative superiority.
- **Intervention assumptions:** Adding an adverse conditional probability of `0.05` where the adaptive policy requests a higher state than the baseline reverses the mean conditional outcome comparison. This is a stress-test assumption, not a measured intervention-risk estimate.

Across ten seeds, each generating 100,000 states, the realised paired difference ranged from **2.534 to 2.711 percentage points**.

Critical-state labels are author-specified and share monitored inputs with the policy. They are not independently measured operational ground truth. The finite sensitivity grids are not an exhaustive global sensitivity analysis.

### Separate temporal logic tests

The main outcome comparison does **not** simulate temporal mission trajectories or apply hysteresis across sampled states.

A separate test evaluates the supervisor on **500 prescribed-score traces of 600 one-second ticks each**.

| Measure | Memoryless supervisor | Hysteretic supervisor |
|---|---:|---:|
| Mean state changes per trace | 97.556 | 5.836 |
| Mean proportion of ticks in S2–S4 | 29.699% | 63.727% |

Hysteresis reduces switching while substantially increasing protected-state occupancy. These are logic-test results, not measurements of aircraft stability, pilot workload, physical transition latency or safe fallback execution.

---

## Reproduce the evaluation

### 1. Download and extract

Download [ESM_1.zip](ESM_1.zip). From the folder containing the downloaded archive:

```bash
python -m zipfile -e ESM_1.zip .
cd Supplementary_Code_S1
```

Keep the downloaded archive unchanged as the reference copy.

### 2. Create an isolated Python environment

The supplied execution used **Python 3.13.5**.

```bash
python -m venv .venv
```

Activate the environment using the command for your operating system.

**Linux/macOS:**

```bash
source .venv/bin/activate
```

**Windows PowerShell:**

```powershell
.\.venv\Scripts\Activate.ps1
```

Install the recorded dependencies:

```bash
python -m pip install -r requirements.txt
```

The supplied requirements record:

```text
numpy==2.3.5
scipy==1.17.0
pandas==2.2.3
matplotlib==3.10.8
```

Exact replay was checked in the recorded environment. A fresh installation on another platform was not independently validated; cross-platform byte-for-byte equality is not guaranteed.

### 3. Run tests, reproduce the study and regenerate numerical figures

Run these commands from `Supplementary_Code_S1`:

```bash
python -m unittest discover -s tests -v

python run_study.py --config config.json --output replay

python verify_outputs.py --results replay

python make_figures.py --results replay --output replay_figures
```

The `replay` directory keeps newly generated analysis outputs separate from the supplied `results` directory.

After dependencies are installed, numerical reproduction requires no external dataset, API, model download or GPU.

`make_figures.py` generates Figures 4–7 and supplementary Figures S1–S3 from the saved numerical outputs. Figures 1–3 are conceptual diagrams with separate artwork source and pre-rendered versions. Their optional regeneration dependencies are described in the package README and supplementary methods.

---

## Package contents

The principal files inside the extracted archive are:

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
├── assure_bvlos/
│   ├── __init__.py
│   └── model.py
├── tests/
│   └── test_model.py
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
├── figures/
└── audit/
```

`results/primary_states.npz` contains the full primary population, raw observables, normalised scores, policy states, probabilities, labels and paired outcomes.

`data_dictionary.csv` documents the saved arrays. The configuration file supplies the generator parameters, normalisations, weights, thresholds, override rules, surrogate coefficients, mitigation assumptions, seeds and sensitivity settings.

---

## Recorded verification

The packaged final audit records:

| Check | Recorded outcome |
|---|---:|
| Implementation tests | 28 passed |
| Output-consistency checks | 65 passed |
| Saved primary arrays matching the same-environment rerun | 29 |
| Result CSV files matching byte for byte | 16 |

Audit details are supplied in `audit/` and the verification files under `results/`.

The configuration SHA-256 recorded for the reported evaluation is:

```text
82ab6791a44b771a66360d719d94c7ca6a800cd127e0f7e7721d3148fa256baa
```

A hash identifies file contents. It does not prove scientific correctness, external preregistration, software qualification or operational safety.

These are recorded implementation and consistency checks, not an independent external validation campaign.

---

## Current figures and historical artwork

The revised figure set is provided in the `figures/` directory inside [ESM_1.zip](ESM_1.zip).

| Figure | Subject |
|---|---|
| **Figure 1** | Proposed ASSURE-BVLOS architecture. |
| **Figure 2** | Authority bands and recovery logic, including latched S4. |
| **Figure 3** | Illustrative safety-case claims and evidence. |
| **Figure 4** | Primary normalised domain distributions. |
| **Figure 5** | Primary paired simulated outcome probabilities. |
| **Figure 6** | Simulated outcome probabilities by archetype. |
| **Figure 7** | Threshold sensitivity of recall and intervention burden. |
| **Figure S1** | Vehicle weighting and the recall–burden trade-off. |
| **Figure S2** | Sensitivity to an adverse-intervention assumption. |
| **Figure S3** | Example prescribed-score authority trace. |

Figures S1–S3 are also included in [ESM_2.pdf](ESM_2.pdf).

**Historical artwork notice:** The older standalone files named `Figure_1_...png` through `Figure_6_...png`, together with the earlier `ASSURE_BVLOS_Graphical_Abstract.png`, belong to the previous manuscript version. They are not the current figures for the replacement evaluation.

Use the revised figures supplied inside `ESM_1.zip`. Historical numerical artwork must not be presented as evidence for the current results.

---

## Scientific scope and responsible use

This study uses synthetic states, engineering normalisations, assumed outcome-surrogate coefficients and prescribed mitigation effects.

The main evaluation omits aircraft dynamics, physical encounter geometry, realistic network time series, additional measurement error, pilot behaviour and common-cause failures. Hard-fault flags exercise decision priority rather than simulate fault-specific crash or recovery physics.

The package does not establish operational accident probabilities, causal intervention effectiveness, safe fallback execution, regulatory acceptance or certification compliance.

Closed-loop simulation, representative monitor testing, hardware- and human-in-the-loop studies, and controlled flight validation remain necessary before operational conclusions can be drawn.

**Do not use this research implementation as the sole basis for real-world flight-safety, termination, certification or deployment decisions.**

---

## Citation and archival status

For the replacement computational evaluation, use the implementation identifier and record the repository commit corresponding to the files used.

Suggested citation:

> Barua, N. (2026). *ASSURE-BVLOS: New Reference Computational Evaluation*. Reference implementation 2.0.0-revision. GitHub. https://github.com/Nick-Barua/ASSURE-BVLOS

The earlier documentation snapshot is identified in the manuscript as:

[Version 1.0.0 — DOI: 10.5281/zenodo.21642250](https://doi.org/10.5281/zenodo.21642250)

**That earlier DOI must not be cited as containing the replacement executable evaluation.**

The supplied revision package has no newly assigned DOI. This README does not assert a new archival deposit or journal acceptance. Any later version-specific archival record should preserve the distinction between the earlier documentation and the replacement evaluation.

---

## AI assistance and author responsibility

Generative AI assistance was used for drafting, mathematical exposition, reference-code generation, test construction and presentation.

The package records the computational checks actually executed. AI output is not treated as an independent source of scientific evidence, and the checks do not replace independent scientific review. Responsibility for the scientific content and final submission remains with the author.

---

## Licence

The repository and reference implementation retain the **BSD 3-Clause License**.

See [LICENSE](LICENSE) and the licence included in `ESM_1.zip` for the applicable terms and warranty disclaimer.

---

## Contact and reproducibility reports

**Nick Barua**  
**ORCID:** [0000-0003-4641-0112](https://orcid.org/0000-0003-4641-0112)  
**Email:** [s.nick.barua@gmail.com](mailto:s.nick.barua@gmail.com)

Report technical issues through [GitHub Issues](https://github.com/Nick-Barua/ASSURE-BVLOS/issues).

Include the package version or repository commit, operating system, Python and dependency versions, command executed, configuration changes and complete error output. Do not include confidential operational data or personal information.
