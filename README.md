## Summary

- The challenge was to maximise eight unknown black-box functions (2D to 8D), submitting one query per function per week.
- Each function started from a small set of initial observations and grew over 11 weekly rounds (R1 to R11) as the portal returned each new output.
- The method was free to choose. Each function uses Bayesian optimisation with Gaussian-process surrogates and acquisition functions (EI, PI, UCB), with TuRBO trust regions and neural-network cross-checks for some functions. The final query was chosen by hand from the candidates each notebook prints.
- Inputs were submitted in the portal's exact format: six decimals, hyphen-separated, within [0, 1).
- The competition has now ended. This repository holds the final pipeline, the data and the documentation.

## Repository structure

```
bbo_final_pipeline/
├── README.md
├── HISTORY.md
├── DATASHEET.md
├── MODEL_CARD.md
├── capstone_presentation.pdf
├── .gitignore
├── notebooks/
│   ├── data_handling.ipynb    # builds data/data.csv from initial_data/ + new_data/
│   ├── f1.ipynb               # optimisation workbook for Function 1
│   ├── f2.ipynb               # ... Function 2
│   ├── f3.ipynb               # ... Function 3
│   ├── f4.ipynb               # ... Function 4
│   ├── f5.ipynb               # ... Function 5
│   ├── f6.ipynb               # ... Function 6
│   ├── f7.ipynb               # ... Function 7
│   ├── f8.ipynb               # ... Function 8
│   └── run_all.py             # executes data_handling.ipynb then f1-f8 in order
└── data/
    ├── data.csv               # harmonised dataset read by f1.ipynb-f8.ipynb
    ├── initial_data/
    │   ├── function_1/initial_inputs.npy, initial_outputs.npy
    │   ├── function_2/initial_inputs.npy, initial_outputs.npy
    │   ├── ...
    │   └── function_8/initial_inputs.npy, initial_outputs.npy
    └── new_data/
        ├── inputs.txt, inputs.json     # each round's submitted inputs
        └── outputs.txt, outputs.json   # each round's returned outputs
```

### Running the notebooks

Each function notebook is self-contained and follows the same pattern: a **DATA LOADING** cell reads `../data/data.csv` and selects the function, then the modelling cells fit surrogate models, search for candidates and print the recommended next query in the portal's submission format (six decimals, hyphen-separated). New rounds appended to `data.csv` flow through on re-run with no code edits.

- Open a notebook from inside `notebooks/` (paths are relative), or run everything with `python run_all.py` from that folder.
- f2 to f8 print tables only; f1 has not yet been simplified and still produces plots.
- Candidates can differ by function: f2 reports local acquisition winners (posterior mean, EI, PI, UCB), f3 searches a consensus of two GPs, f4 compares GP, neural-network and TuRBO proposals, f6 and f7 are TuRBO-1 (scikit-learn and GPyTorch respectively), f5 searches the high-yield corner with raw and log GPs, and f8 only moves off the incumbent if the predicted gain exceeds the model's leave-one-out error. The final query is chosen by hand from these printed options.

### What was this challenge about?

This capstone project mimicked a Bayesian optimisation-style competition: find the maximum of eight unknown black-box functions. The equations and shapes of the functions were hidden. Only a small set of initial data and the ability to make informed queries were available.

The code for each function is contained in separate workbooks named f1.ipynb, f2.ipynb... f8.ipynb.

Each function simulates a real-world task, described below and at the beginning of each workbook, where evaluations are expensive or limited. Perfect solutions were not expected; the aim was to apply sound search strategies and reflect on them as the data grew.

The eight functions are synthetic black boxes: unknown mathematical functions that accept inputs and return a single output. Every function is a **maximisation** problem, with increasing dimensionality (2D to 8D), and each began with a small set of known (x, y) observations.

Each week:

1. The existing data was reviewed (it grew by one point per function each week).
2. A new input (x) was chosen for each function.
3. The input was submitted through the capstone project portal in the correct format.
4. The new output (y) was returned once the submission was processed.
5. The dataset was updated and the strategy revised.
6. Each round was followed by a reflection on the method used and why, whether the query was exploration or exploitation, what the latest result showed, and the strategy for the next round.

The process ran for 11 weekly rounds, so each function ends with its initial observations plus 11 queried points.

## Techniques used

Any ML method could be used to decide the next query, for example random search, grid search, Bayesian optimisation with a GP and acquisition functions such as UCB, manual reasoning from plots, or custom surrogate models. This repository uses GP-based Bayesian optimisation throughout, with TuRBO trust regions and neural-network cross-checks for some functions (see "Running the notebooks" above). The task did not require submitting an optimiser, building a full optimisation model from scratch, or finding the global maximum of every function.

All eight functions are maximisation problems.

## Function Shapes

Observations per function: initial (round 0) and final (after 11 weekly rounds).

| Function | Inputs | Initial points | Final points |
|---|---|---|---|
| 1 | 2 | 10 | 21 |
| 2 | 2 | 10 | 21 |
| 3 | 3 | 15 | 26 |
| 4 | 4 | 30 | 41 |
| 5 | 4 | 20 | 31 |
| 6 | 5 | 20 | 31 |
| 7 | 6 | 30 | 41 |
| 8 | 8 | 40 | 51 |

## Function Descriptions

Function 1: Detect likely contamination sources in a two-dimensional area, such as a radiation field, where only proximity yields a non-zero reading. The system uses Bayesian optimisation to tune detection parameters and reliably identify both strong and weak sources.

Function 2: Imagine a black box, or a mystery ML model, that takes two numbers as input and returns a log-likelihood score. Your goal is to maximise that score, but each output is noisy, and depending on where you start, you might get stuck in a local optimum. To tackle this, you use Bayesian optimisation, which selects the next inputs based on what it has learned so far. It balances exploration with exploitation, making it well suited to noisy outputs and complex functions with many local peaks.

Function 3: You’re working on a drug discovery project, testing combinations of three compounds to create a new medicine. Each experiment is stored in initial_inputs.npy as a 3D array, where each row lists the amounts of the three compounds used. After each experiment, you record the number of adverse reactions, stored in initial_outputs.npy as a 1D array. Your goal is to minimise side effects; in this competition, it is framed as maximisation by optimising a transformed output (e.g. the negative of side effects). 

Function 4: Address the challenge of optimally placing products across warehouses for a business with high online sales, where accurate calculations are costly and only feasible biweekly. To speed up decision-making, an ML model approximates these results within hours. The model has four hyperparameters to tune, and its output reflects the difference from the expensive baseline. Because the system is dynamic and full of local optima, it requires careful tuning and robust validation to find reliable, near-optimal solutions.

Function 5: You’re tasked with optimising a four-variable black-box function that represents the yield of a chemical process in a factory. The function is typically unimodal, with a single peak where yield is maximised. Your goal is to find the optimal combination of chemical inputs that delivers the highest possible yield, using systematic exploration and optimisation methods.

Function 6: You’re optimising a cake recipe using a black-box function with five ingredient inputs, for example flour, sugar, eggs, butter and milk. Each recipe is evaluated with a combined score based on flavour, consistency, calories, waste and cost, where each factor contributes negative points as judged by an expert taster. This means the total score is negative by design. To frame this as a maximisation problem, your goal is to bring that score as close to zero as possible or, equivalently, to maximise the negative of the total sum.

Function 7: You’re tasked with optimising an ML model by tuning six hyperparameters, for example learning rate, regularisation strength or number of hidden layers. The function you’re maximising is the model’s performance score (such as accuracy or F1), but since the relationship between inputs and output isn’t known, it’s treated as a black-box function. Because this is a commonly used model, you might benefit from researching best practices or literature to guide your initial search space. Your goal is to find the combination of hyperparameters that yields the highest possible performance.

Function 8: You’re optimising an eight-dimensional black-box function, where each of the eight input parameters affects the output, but the internal mechanics are unknown. Your objective is to find the parameter combination that maximises the function’s output, such as performance, efficiency or validation accuracy. Because the function is high-dimensional and likely complex, global optimisation is hard, so identifying strong local maxima is often a practical strategy. For example, imagine you’re tuning an ML model with eight hyperparameters: learning rate, batch size, number of layers, dropout rate, regularisation strength, activation function (numerically encoded), optimiser type (encoded) and initial weight range. Each input set returns a single validation accuracy score between 0 and 1. Your goal is to maximise this score.