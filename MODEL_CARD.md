# Model Card for the BBO Optimisation Approach

## 1. Overview

**Approach name:** Function-Specific Surrogate-Assisted Bayesian Optimisation

**Model type:** Sequential black-box optimisation using probabilistic surrogate models and alternative regression approaches.

**Version:** Round 10

**Primary tools:** Python, Jupyter, scikit-learn, NumPy, Pandas and SciPy, with selective LLM-assisted coding and analysis.

The approach is not a single fixed model. It is a framework for selecting and refining optimisation methods according to the observed characteristics of each function, recognising their highly idiosyncratic behaviour.

## 2. Intended Use

The approach is designed for maximising expensive-to-evaluate black-box functions where the underlying mathematical relationships and gradients are unknown.

It is particularly relevant where:

- Evaluation budgets are limited.
- The number of observations is relatively small.
- Functions have different dimensionalities and structural characteristics.
- Decisions must balance exploration and exploitation.

The approach is less suitable where the underlying functions are highly discontinuous, observations are too sparse to support reliable surrogate modelling, or model uncertainty is poorly calibrated.

It should not be applied automatically to real-world optimisation problems without assessing the relevant assumptions and constraints.

## 3. Optimisation Strategy and Evolution

The methodology evolved considerably across the ten rounds.

### Initial approach
The initial strategy was largely exploratory and heuristic. The observations were examined to identify promising regions and infer relationships between inputs and outputs.

Although this produced some improvements, it was difficult to assess uncertainty or determine whether a particular query was genuinely preferable to alternatives.

### Introduction of Gaussian Processes and Bayesian optimisation
GP surrogate models were subsequently introduced, principally using scikit-learn.

These allowed estimation of the underlying functions and generation of predictions for unobserved coordinates. The methodology included experimentation with alternative kernels, hyperparameter optimisation and acquisition functions. The main advantage was the ability to combine predicted performance with an estimate of uncertainty.

However, sophisticated models could fit the observed data without necessarily producing better queries.

### Function-specific modelling
As the challenge progressed, it became clear that the eight functions were highly idiosyncratic.

- **F1:** Extreme variation in output magnitude, requiring careful local modelling.
- **F2:** Multiple promising regions and limitations in GP predictive performance.
- **F3:** Apparent observation noise, complicating model fitting and validation.
- **F4:** Substantial improvements but considerable sensitivity to selected coordinates.
- **F5:** Strong directional and boundary-related behaviour.
- **F6:** A balance between local refinement and the possibility of better regions elsewhere.
- **F7:** Successive improvements through local refinements, particularly in later rounds.
- **F8:** Relatively simple structural approaches and alternative regression models sometimes more useful than increasingly complex GP specifications.

### Validation and LLM-assisted analysis
Model diagnostics and out-of-sample validation, particularly leave-one-out cross-validation (LOOCV), were increasingly used to compare modelling approaches.

LLMs were used selectively to assist with developing code, interpreting diagnostics, evaluating alternative models and proposing query coordinates. However, LLMs often made basic errors despite successfully performing complex coding or analytical tasks. This led to a more structured approach involving detailed prompts, challenging recommendations and occasionally using one LLM to review another.

The final strategy combined numerical optimisation with LLM-assisted analysis, while retaining the observed data as the principal constraint.

## 4. Performance

The primary performance measure was the maximum observed function output.

For each function, the analysis compared:

- The best observed result from previous rounds.
- The result of the latest query.
- The best result achieved across all observations.

The analysis also considered whether a query improved the incumbent and whether it provided useful information about the underlying function. For surrogate models, relevant diagnostics included prediction errors, LOOCV performance and the consistency of recommendations across alternative specifications.

### Performance across the functions
The methodology increasingly involved selecting an appropriate model for each function rather than applying a universal pipeline. The results included substantial improvements across several functions, although performance was uneven.

The best observed results through Round 10 are:

| Function | Best observed result | Last round achieved |
|----------|----------------------|---------------------|
| F1 | 3.50 × 10⁻⁶ | 10 |
| F2 | 0.668242 | 10 |
| F3 | −0.007172 | 9 |
| F4 | 0.730826 | 8 |
| F5 | 8662.4825 | 10 |
| F6 | −0.068174 | 9 |
| F7 | 3.267413 | 10 |
| F8 | 10.000000 | 10 |

The function-specific observations are summarised below.

- **F1:** An extremely narrow region of positive outputs, with successive refinements producing incremental improvements and the best result achieved in Round 10.
- **F2:** Multiple promising regions, with considerable variation between evaluations. Round 10 produced the best result, indicating that further improvements remained possible.
- **F3:** Challenging due to apparent observation noise and inconsistent results for identical inputs. The best result was achieved in Round 9, followed by a weaker result in Round 10.
- **F4:** Considerable sensitivity to input coordinates, with substantial improvements during later rounds. The best result was achieved in Round 8, with Round 10 producing a result close to the historical best.
- **F5:** Strong boundary-related behaviour, with the highest outputs achieved when all input variables approached 1. The best result in Round 10 matched an earlier round, suggesting that the maximum may have been reached.
- **F6:** Challenges in balancing local refinement with broader exploration. A substantial improvement was achieved in Round 9, with Round 10 producing a slightly weaker result.
- **F7:** Consistent improvements through successive local refinements, with the best result achieved in Round 10, suggesting that further optimisation may still be possible.
- **F8:** A relatively stable optimum, with the maximum observed output of 10 achieved in Round 10 and matched in an earlier round. Subsequent evaluations around this region produced very similar results, suggesting that the maximum may have been reached.

## 5. Assumptions and Limitations

### Key assumptions
A central assumption is that the available observations contain sufficient information to infer useful relationships between inputs and outputs. GPs make assumptions about covariance structure and smoothness through their kernels. These assumptions may be inappropriate where functions are highly irregular, non-stationary or where observation noise is not adequately modelled.

Another assumption is that surrogate uncertainty estimates are sufficiently reliable to guide exploration and exploitation. With limited observations, this may not always be the case.

### Main limitations and failure modes
- **Limited observations:** Surrogate models may fit the observed data well while providing misleading predictions in unexplored regions.
- **Sampling bias:** Concentration of queries around promising regions improves local optimisation but reduces coverage of the broader search space.
- **Model complexity:** Additional hyperparameters, transformations and more sophisticated algorithms can improve apparent model fit without improving query performance.
- **Uncertainty calibration:** GP uncertainty estimates may be unreliable when observations are sparse or kernel assumptions are inappropriate.
- **LLM-assisted analysis:** Risks include confirmation bias, inconsistent recommendations and errors that are not immediately apparent.

These limitations increasingly favoured robust, validated approaches over unnecessary model complexity.

## 6. Ethical Considerations and Transparency

### Real-world adaptation
Although the capstone involves abstract mathematical functions, the underlying issues have direct relevance to real-world decision-making.

For example, in agri-food technology, optimisation may involve biological systems where outcomes are stochastic and decisions have financial, operational or environmental consequences.

In such applications, an optimisation model should not be treated as reliable simply because it produces precise numerical predictions. Understanding uncertainty, documenting assumptions and, importantly, retaining human oversight are essential.

### Reproducibility

**Current documentation**
- Jupyter notebooks contain the datasets, modelling code, diagnostics and visualisations.
- The final code represents the culmination of ten rounds of iterative development, with function-specific optimisation pipelines.
- The final models and associated analyses provide a basis for technical reproduction, subject to the availability of the relevant data, dependencies and model settings.

**Limitations**
- Earlier experimental approaches were frequently discarded or superseded.
- Methodological changes and query-selection decisions were not systematically documented across all rounds.
- Consequently, the complete historical decision-making process cannot be reconstructed from the final notebooks alone.

**Assessment**
- The primary objective was to develop effective optimisation approaches rather than preserve a complete record of the learning process.
- Comprehensive documentation of earlier rounds would improve historical transparency but offer limited additional insight into the final models and would cloud understanding of the pipeline.
- Full historical reproducibility would require round-specific model specifications, hyperparameters, validation results and query-selection rationale.
