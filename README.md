# LoanChecker

**An explainable AI credit-risk model with an integrated fairness audit, investigating age disparities in lending decisions and testing interventions to improve fairness without materially increasing lending costs.**

## Overview

LoanChecker investigates a fundamental problem in algorithmic lending: can a credit-risk model accurately identify applicants likely to repay while systematically disadvantaging younger applicants?

Using the UCI German Credit dataset, we develop and compare machine-learning models, evaluate their predictive performance under an asymmetric lending-cost framework, and conduct a fairness audit examining differences in approval outcomes across age groups.

We then investigate whether alternative decision thresholds can reduce disparities without materially increasing the lender's expected costs.

**This is an experimental research prototype, not a production credit-scoring system.**

## Research questions

1. How effectively can machine-learning models distinguish between applicants who repay and those who default?
2. Do applicants under 25 experience systematically lower approval rates?
3. Does removing age from the model eliminate the observed disparity?
4. Can alternative decision thresholds improve fairness while maintaining comparable lending costs?
5. What are the limitations and potential consequences of these interventions?

## Methodology

### Dataset

The project uses the German Credit dataset, comprising 1,000 historical credit applicants:

- 700 applicants classified as good credit risks.
- 300 applicants classified as bad credit risks.
- 149 applicants under the age of 25.

The dataset contains applicant characteristics and historical credit-risk classifications. It originates from historical German lending data from the 1970s.

The dataset is relatively small, historically dated and not representative of contemporary lending populations.

### Models

We compare three classification approaches:

- Logistic regression
- Random forest
- Gradient boosting

Logistic regression is selected as the principal model because of its interpretability and comparatively controlled complexity.

### Cost-sensitive decision-making

The project applies an asymmetric cost framework in which the cost of a bad lending decision is five times the cost of rejecting a good applicant.

Under this simplified framework, the decision threshold is:

\[
t = \frac{5}{5+1} \approx 83.3\%
\]

This threshold reflects the assumed relative costs; it is not a universal lending threshold.

### Fairness audit

We investigate approval-rate differences between applicants under 25 and those aged 25 or above.

The analysis includes:

- Overall approval-rate disparities.
- Approval rates among applicants classified as good risks.
- Sensitivity to alternative age cut-offs.
- The effect of removing age as a model input.
- Proxy discrimination analysis.
- Comparisons between applicants with similar financial characteristics.
- Calibration and tail-risk sensitivity analysis.

### Fairness intervention

We test a separate decision-threshold intervention for younger applicants.

The objective is to reduce disparities in approval outcomes while assessing the impact on expected lending costs and the treatment of other applicants.

This is an experimental intervention, not a recommendation for real-world lending policy.

## Key findings

The following are the principal results reported by our analysis.

| Metric | Original model | Fairness intervention |
|---|---:|---:|
| Model cost | 547 | 547 |
| Accuracy at selected threshold | 58.5% | 59.7% |
| Good-risk approval gap | 19.5 percentage points | 2.4 percentage points |

Additional findings:

- The original model approves approximately 28.4% of under-25 applicants classified as good risks, compared with 47.9% of applicants aged 25 or above in the same category.
- Removing age alone reduces the overall approval gap only from 19.2 to 17.9 percentage points.
- The tested intervention results in 18 additional approvals for younger applicants, 15 of whom were classified as good risks.
- The intervention also changes the allocation of approvals among older applicants, which is an important distributional trade-off.

These findings illustrate why simply removing a protected characteristic from a model may not eliminate disparities: other variables can act as proxies for that characteristic.

The results should be interpreted in the context of the dataset, modelling assumptions and experimental design.

## Reproducing the analysis

The main analysis is provided in the Jupyter notebook:

[`loan_fairness_audit_FINAL_v3.ipynb`](loan_fairness_audit_FINAL_v3.ipynb)

Execution results are also available:

[`LoanCheck_all_results.txt`](LoanCheck_all_results.txt)

### Running in Google Colab

1. Download the notebook or open it through GitHub.
2. Upload it to [Google Colab](https://colab.research.google.com/).
3. Run the notebook cells in order.
4. Install any dependencies requested by the notebook.
5. Review the generated metrics, fairness analysis and validation outputs.

The results text file provides a record of the reported execution.

## Limitations

This project has several important limitations:

- **Historical data:** The dataset represents German lending data from the 1970s and may not reflect current economic or lending conditions.
- **Sample size:** The dataset contains only 1,000 applicants, including a relatively small under-25 subgroup.
- **Missing information:** The dataset does not contain all information that a contemporary lender might use, including comprehensive income and expenditure data.
- **Selection bias:** Historical lending data does not provide a complete picture of applicants who were previously rejected.
- **Default-rate assumptions:** The dataset's 30% bad-risk proportion differs from the approximately 5% realistic default-rate scenario explored in the sensitivity analysis.
- **Fairness measurement:** Approval-rate parity alone does not establish that a lending decision is fair.
- **Legal considerations:** A separate age-based decision threshold raises significant legal and regulatory questions. A reduction in measured disparity does not establish that an intervention is lawful or appropriate.
- **Model risk:** The results depend on the chosen model, cost assumptions, validation approach and threshold-selection procedure.

The project should therefore be understood as an investigation into algorithmic fairness, not evidence that the tested intervention is suitable for deployment.

## Technology

The project uses Python and machine-learning methods within a Jupyter/Google Colab environment.

**Add the exact libraries and versions used in the final notebook here**, including the relevant versions of scikit-learn, pandas, NumPy and other dependencies.

## Sources and acknowledgements

- **Dataset:** UCI Machine Learning Repository — German Credit dataset. [Dataset reference](https://archive.ics.uci.edu/dataset/144/statlog+german+credit+data).
- **AI tools:** [List every AI tool used, with links and a description of its contribution.]
- **Additional resources:** [List any tutorials, code references, documentation or other external resources used.]

All team contributions and external assistance should be disclosed accurately.

## Project links

- **Source code:** This repository.
- **Interactive project:** [LoanChecker website](https://claude.ai/artifact/Nh2A6yfdTzJ1XhStp2k1Fs)
- **Execution results:** [`LoanCheck_all_results.txt`](LoanCheck_all_results.txt)
- **Research write-up:** [Add the final report link.]

## Disclaimer

LoanChecker is an experimental research prototype developed for the Veritas AI Hackathon. It is not a production-ready credit-scoring system and does not constitute financial advice or a recommendation to lend to or refuse any individual.

The findings are educational and research-oriented. They should not be used to make actual credit decisions.
