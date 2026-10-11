# Week 1 Optimization Analysis & Reflection Report

## Function-by-Function Technical Briefs

### Function 1 (2D)

A Gaussian Process (GP) with an RBF kernel was fitted to 10 initial points and queried using Upper Confidence Bound (UCB). The process behaves like a proximity sensor where most initial samples returned near-zero values. With a flat GP mean surface, an exploratory query in the mid-upper region returned $y = -0.015$. High exploration must continue until a non-zero signal emerges to guide local exploitation.

### Function 2 (2D)

A GP with an RBF kernel and noise estimator identified two potential modes: a primary peak in the upper-right quadrant and a secondary bump in the lower-mid region. Prioritizing the primary peak in the upper-right corner returned $y = -0.006$. Because this primary query underperformed, subsequent rounds should evaluate the secondary mode in the lower-mid region to verify the true global peak.

### Function 3 (3D)

A GP with an RBF kernel trained on 15 initial observations selected a mild exploratory query in the central region, returning $y = -0.013$. Distance checks confirmed no competing modes within the current data. Optimization should move from basic grid evaluations to continuous gradient-based multi-restart acquisition searches.

### Function 4 (4D)

A GP model evaluated on negative UCB converged toward zero noise and selected an exploitation query near the center-left of the domain, returning $y = -0.137$. While optimizer restarts showed numerical stability, multi-modal scanning is required to avoid missing alternative basins.

### Function 5 (4D)

Initial observations spanned from $0.110$ to $1089.000$, requiring a logarithmic target transformation. Acquisition optimization consistently converged to the upper boundary of the fourth input dimension ($x_4 \to 1.0$). A boundary query returned $y = 1410.188$, confirming strong gains at the domain edge. Future sampling should exploit this upper boundary manifold.

### Function 6 (5D)

A GP model captured non-trivial observation noise, aligning with domain characteristics. An exploratory query placed broadly across mid-to-upper coordinates returned $y = -0.306$, improving upon the previous baseline of $y = -0.714$. This point should be used to update the mean surface for local refinement.

### Function 7 (6D)

Target values were right-skewed and log-transformed prior to GP modeling. An exploratory query positioned in lower-to-mid dimensions with a high sixth dimension returned $y = 2.309$, exceeding the initial maximum of $1.365$. The agent should transition toward local exploitation around this region.

### Function 8 (8D)

Initial modeling using GP, Linear Regression, and Random Forest models independently flagged $x_1$, $x_3$, and $x_7$ as dominant negative drivers. Enforcing lower boundaries on these negative inputs while exploring higher values for $x_5$ returned $y = 9.800$. Future optimization should keep $x_1, x_3, x_7 \le 0.1$ constrained and search along the remaining active axes.

---

# Week 2 Optimization Analysis & Reflection Report

## Function-by-Function Technical Briefs

### Function 1 (2D)

With 11 observations, Matérn and RBF kernels performed similarly, showing a shrinking length scale. Shifting the query further into the upper region returned a near-zero response ($y \approx 0.000$), improving slightly on Week 1 ($y = -0.015$) but confirming no direct signal hit. Broad exploration must continue until an active basin is intersected.

### Function 2 (2D)

Rather than continuing near the disappointing primary peak, Week 2 evaluated the secondary candidate mode located in the lower-right area. This resulted in a significant breakout to $y = 0.589$, confirming that this secondary region contains the true global maximum. Optimization should shift fully toward local exploitation around this area.

### Function 3 (3D)

Perturbing the second dimension downward while holding the other axes near Week 1 levels resulted in $y = -0.030$, declining from the previous $y = -0.013$. This indicates a performance drop along this axis. Future queries should center back on the Week 1 region and adjust the local search radius.

### Function 4 (4D)

Moving slightly outward from the central region caused performance to drop sharply to $y = -1.995$ compared to Week 1 ($y = -0.137$). This indicates movement into an inferior local basin. Local modeling should anchor back to the Week 1 location while scanning for alternative modes.

### Function 5 (4D)

Maintaining the upper boundary on the fourth dimension while slightly lowering the first dimension returned $y = 1029.657$, remaining strong but below the Week 1 peak of $y = 1410.188$. This confirms the upper boundary of $x_4$ is critical, but favors keeping $x_1$ moderately higher. Future sampling should remain tightly focused on this boundary zone.

### Function 6 (5D)

An exploratory query driving the fifth dimension to its lower boundary regressed to $y = -1.175$ from Week 1 ($y = -0.306$). Noise remains a factor. Queries should pull back toward the Week 1 coordinate area to refine the local mean.

### Function 7 (6D)

Driving the first dimension to its lower boundary returned $y = 1.227$, below the Week 1 peak of $y = 2.309$. Both rounds confirm an unbounded objective above $1.000$. Bayesian optimization should focus on the local neighborhood of the Week 1 query.

### Function 8 (8D)

Pinning $x_1$ and $x_3$ to zero while adjusting intermediate dimensions returned $y = 9.722$, showing strong consistency with Week 1 ($y = 9.800$). Hard constraints should remain on $x_1, x_3, x_7 \le 0.1$ while tuning the remaining parameters.

---

# Week 3 Optimization Analysis & Reflection Report

## Function-by-Function Technical Briefs

### Function 1 (2D)

A previous non-monotonic transform was removed, and simple parametric dipole approximations were rejected due to poor fit. An unsigned log-magnitude GP was retained. A diagnostic query placed near the center of the domain was submitted to test whether a strong positive source, negative source, or background signal existed in that region.

### Function 2 (2D)

Modeling revealed that the second input dimension is largely inactive, while the first dimension exhibits periodic behavior. The periodic GP substantially outperformed stationary kernels, retrospectively explaining earlier performance drops as predictable periodic troughs. A query testing this periodic mode along the first dimension (predicted $\mu = 0.631$, $\sigma = 0.028$) was submitted in an unexplored gap of the inactive second dimension.

### Function 4 (4D)

The sharp drop in Week 2 indicated local volatility that a single length scale struggled to capture. Trust-region optimization around the Week 1 incumbent reversed the failed Week 2 moves across the first three dimensions (predicted $\mu = -0.450$, $\sigma = 0.643$, UCB $= 0.810$). If performance drops again at this step size, future queries should restrict to a smaller trust region.

### Function 5 (4D)

Switching to a fitted Box-Cox transformation improved model fit over simple log transforms. Pure posterior-mean exploitation and constrained acquisition indicated that $x_2$ and $x_3$ should increase while holding $x_4$ near the upper boundary. The submitted boundary query returned $y = 3290.429$, a major improvement over the prior incumbent ($y = 1410.188$), confirming the upper-corner trajectory.

### Function 6 (5D)

Separate input length scales confirmed signal across the inputs, while model calibration required inflating uncertainty bounds. A conservative step decreasing $x_2$ and $x_5$ while increasing $x_3$ and $x_4$ was submitted, aligning with gradient estimates and prior failure analysis.

### Function 8 (8D)

Analysis revised earlier hard constraints, showing that only $x_1$ requires pinning to its lower wall, whereas $x_3$ and $x_7$ favor interior values, and $x_5$ benefits from higher values. An ensemble model predicting $\mu = 10.059$ ($\sigma = 0.091$, $2\sigma$ bound of $9.882$) selected an interior query with low $x_1$ and high $x_5$ and $x_8$ to test whether this revised structure outperforms the incumbent ($y = 9.800$).

---

# Week 4 Optimization Analysis & Reflection Report

## Function-by-Function Technical Briefs

### Function 1 (2D)

Week 4 located a clear positive region, returning a non-zero positive response ($y \approx 0.000$, substantially higher than historical baselines) in the upper-right area. Strategy shifted from broad search to local exploitation, selecting a candidate that stays tightly within this verified positive basin rather than exploring uncertain boundaries.

### Function 2 (2D)

Diagnostics indicated sharp local variations, supported by short length scales across multiple kernel choices. Expected Improvement selected a balanced query in the upper-right quadrant (predicted $y = 0.636$, $\sigma = 0.115$) to exploit the proven peak while assessing local basin width.

### Function 3 (3D)

The provided Week 4 Function 3 section duplicates the Function 2 analysis and coordinates. A separate Function 3 evaluation is needed to complete this brief without extrapolating unsupported data.

### Function 4 (4D)

Following a validated local improvement to $y = 0.310$, optimization shifted toward tight local refinement. Combining individual dimension profiles favored a modest adjustment to $x_2$ while holding other dimensions close to their 1D optima, maintaining a small step distance from the incumbent basin.

### Function 5 (4D)

A log-target GP substantially outperformed raw models ($R^2 = 0.990$ vs $0.850$). Both models structurally agreed that the optimum lies at the upper domain boundary where $x_2$, $x_3$, and $x_4$ approach their maximums, confirming the upper corner as the primary exploitation target.

### Function 6 (5D)

Line search analysis showed a smooth, shallow optimum with small differences across candidate points relative to predictive uncertainty. A balanced candidate was chosen near earlier baselines, preserving key dimensions while applying conservative adjustments along axes with clear local evidence.

### Function 7 (6D)

Optimization transitioned to local hyperparameter refinement around the incumbent. Independent models agreed on shifting slightly lower in $x_1, x_2, x_4$, holding $x_3$ and $x_6$ steady, and slightly increasing $x_5$, supporting fine-scale local exploitation rather than aggressive boundary exploration.

### Function 8 (8D)

With 44 observations, the incumbent remained near $y = 9.955$, while a nearby step yielded $y = 9.917$, indicating a wide, high-performing plateau. The GP showed strong out-of-sample accuracy ($R^2 = 0.987$). Cross-model consensus between GP variants and neural network surrogates supported a controlled move within this shared plateau rather than uncertain boundary extrapolation.