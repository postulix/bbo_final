# Datasheet for the BBO Capstone Dataset

## 1. Motivation

### Origin
The dataset was created as part of the 2026 Imperial College Machine Learning and AI capstone challenge, involving the optimisation of eight unknown black-box functions under a limited evaluation budget.

The objective was to maximise each function by progressively selecting input coordinates, observing the corresponding outputs and using those observations to inform subsequent queries.

### Task
The dataset supports sequential black-box optimisation, particularly Bayesian optimisation (BO) using Gaussian Process (GP) surrogate models. It supports comparing alternative modelling approaches, examining uncertainty, identifying promising regions and evaluating the exploration/exploitation trade-off.

An important part of the exercise was understanding how different optimisation approaches perform when observations are limited and the underlying function is unknown.

## 2. Composition

### Datasets
The dataset contains input coordinates and corresponding scalar outputs for eight functions.

Each function has a different dimensionality:

| Function | Input dimensions | Initial observations |
|----------|------------------|----------------------|
| F1 | 2 | 10 |
| F2 | 2 | 10 |
| F3 | 3 | 15 |
| F4 | 4 | 30 |
| F5 | 4 | 20 |
| F6 | 5 | 20 |
| F7 | 6 | 30 |
| F8 | 8 | 40 |

The initial observations were provided by the programme. Additional observations were generated through successive weekly submissions. All input variables are bounded between 0 and 1, with coordinates specified to six decimal places.

### Data format
The original data is stored in NumPy files, with subsequent queries and results recorded in text and JSON formats. These were subsequently consolidated into a harmonised CSV dataset containing the function identifier, round, input coordinates and observed output. This provides a consistent dataset for modelling and analysis while preserving the original observations separately.

### Known gaps
The dataset is relatively sparse, particularly for higher-dimensional functions. There is also a deliberate sampling bias towards promising regions. As the optimisation progressed, queries increasingly concentrated around areas that had previously produced strong results. Consequently, substantial portions of the search space remain unexplored.

Some functions also demonstrated unusual characteristics, including extremely small output values, multiple promising regions and apparent stochasticity, demonstrated by functions providing differing results for identical input variables across different submissions. Specifically, F3 returned different outputs for an identical input, suggesting that observation noise needed to be considered.

## 3. Collection Process

### Query generation
The approach evolved considerably over the ten rounds, as follows.

- Initially, query selection was relatively heuristic during the learning phase. This relied on examining observations, identifying patterns and selecting promising coordinates.
- Subsequently, GP surrogate models and Bayesian optimisation techniques were introduced to estimate the underlying functions and identify candidate queries.
- As more observations became available, the approach became increasingly function-specific.
- Rather than applying a single GP configuration to every function, the methodology expanded to include comparisons of kernels, hyperparameters, alternative surrogate models and acquisition strategies.
- Out-of-sample diagnostics, including leave-one-out cross-validation (LOOCV), were increasingly used to assess model reliability.
- LLMs were used selectively to assist with coding, interpreting diagnostics, comparing modelling approaches and challenging recommendations.
- However, final query selection remained a judgement based on the observed data and the available evidence.

### Time frame
The dataset has so far been developed over ten successive rounds of the capstone challenge, with one additional query permitted per function in each round.

## 4. Preprocessing and Uses

### Preprocessing applied
The original inputs were already normalised to the interval [0,1].

Depending on the function, preprocessing and modelling included:

- Standardisation of output variables where appropriate.
- Examination of output distributions and extreme values.
- Consideration of transformations for highly skewed functions.
- Assessment of observation noise.
- Comparison of alternative kernel specifications and hyperparameters.

Importantly, transformations were not applied uniformly. They were considered where there was evidence that they could improve modelling performance.

### Intended use
The dataset is intended for:

- Sequential black-box optimisation.
- Training and evaluating surrogate models.
- Comparing alternative optimisation approaches according to the idiosyncrasies of individual functions.
- Investigating exploration/exploitation trade-offs.
- Assessing how additional observations affect model performance.

The dataset should not be used to make definitive claims about the global structure of the underlying functions. The relatively small and non-random sample also limits its suitability for conventional supervised learning or for drawing general conclusions about the performance of particular optimisation algorithms.

Also, results should not be generalised to unrelated optimisation problems without further validation.

## 5. Distribution and Maintenance

### Dataset
The dataset is maintained locally within the BBO project directory.

The original observations are preserved separately from subsequent submissions, while a consolidated CSV provides the working dataset. The project also contains Jupyter notebooks and supporting Python code used for modelling and optimisation.

### Terms of use
The dataset was supplied and developed for educational purposes within the capstone programme.

### Maintenance
The dataset is maintained by the project author, who incorporates new observations, validates the data and maintains the modelling notebooks.
