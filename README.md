# AI R&D Assignment — Fitting a Parametric Curve

Find the unknown parameters $\theta,\ M,\ X$ of the parametric curve

$$x = t\cos(\theta) - e^{M|t|}\,\sin(0.3t)\sin(\theta) + X$$

$$y = 42 + t\sin(\theta) + e^{M|t|}\,\sin(0.3t)\cos(\theta)$$

given 1500 points (`xy_data.csv`) that lie on the curve for $6 < t < 60$, within the ranges
$0° < \theta < 50°$, $\ -0.05 < M < 0.05$, $\ 0 < X < 100$.

## ✅ Final answer

| parameter | value |
|---|---|
| **θ** | **0.5236 rad = 30.0° ( = π/6 )** |
| **M** | **0.0300** |
| **X** | **55.00** |

Submission string (Desmos / LaTeX format):

```
\left(t*\cos(0.52359)-e^{0.03\left|t\right|}\cdot\sin(0.3t)\sin(0.52359)+54.998,42+t*\sin(0.52359)+e^{0.03\left|t\right|}\cdot\sin(0.3t)\cos(0.52359)\right)
```

**Error (the assessment metric):** total L1 distance between 1500 uniformly sampled points of the fitted curve and their nearest data points = **40.08** (mean **0.0267** per sampled point). This residual is pure sampling gap.


## Approach (full story in the notebook)

Everything is in [`AI_R&D Assignment .ipynb`](AI_R&D%20Assignment%20.ipynb), written step by step with the maths explained in markdown before each piece of code.

1. **Understand the equation.** The curve is a base wiggle $(t,\ e^{M|t|}\sin 0.3t)$ passed through a 2-D rotation matrix $R(\theta)$ and shifted by $(X, 42)$ — so θ rotates the curve, M grows/shrinks the wiggles, X slides it horizontally. Playing with Desmos sliders gave a first hand guess (θ ≈ 0.52, M ≈ 0.03, X ≈ 54.8).
2. **Define the error.** We don't know which data point belongs to which $t$, so for each of 1500 uniformly sampled predicted points we take the L1 distance to its *nearest* data point and sum:

$$E(\theta,M,X)=\sum_{i=1}^{N}\ \min_{j}\Big(\big|x_i^{pred}-x_j^{actual}\big|+\big|y_i^{pred}-y_j^{actual}\big|\Big)$$

3. **Optimize (derivative-free, since E contains `min` and `abs`).** All methods hand-written with plain NumPy:

| method | total L1 error | note |
|---|---|---|
| hand guess (Desmos) | 106.53 | starting point |
| grid search (coarse, whole box) | 25.819 | finds the right valley |
| hill climbing | 25.812 | refines the grid winner |
| simulated annealing | 27.67 | started from box centre — finds the same valley on its own |
| Nelder–Mead (SciPy) | **25.812** |

All five methods independently agree on θ = 30°, M = 0.03, X = 55 — strong evidence it is the global minimum. The remaining error shows no trend along $t$:

## Repository contents

| file | what it is |
|---|---|
| `AI_R&D Assignment .ipynb` | the full solution notebook (maths + code + plots), fully executed |
| `xy_data.csv` | the given 1500 data points |

## How to run

```bash
pip install numpy pandas matplotlib scipy
jupyter notebook "AI_R&D Assignment .ipynb"  
```

All random methods use fixed seeds, so re-running reproduces the exact numbers above.

## References

- NumPy docs (broadcasting, `default_rng`) — numpy.org/doc
- pandas docs (`read_csv`) — pandas.pydata.org/docs
- SciPy reference (`spatial.cKDTree`, `optimize.minimize`,`optimize.minimize_scalar`) — docs.scipy.org
  
