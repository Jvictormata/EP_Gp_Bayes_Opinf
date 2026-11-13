# Energy-preserving Bayesian operator inference

Code reproducing the numerical experiments of

> J. V. Galvão da Mata and M. Guo, *Energy-preserving Bayesian model reduction:
> closed-form structured posteriors and probabilistic stability certificates for
> quadratic operator inference.*

Everything in the paper is produced by six Jupyter notebooks in `notebooks/`. They are self-contained: each one runs top to bottom and imports nothing from this repository.

## Layout

```
notebooks/
  0 - Data generation - Forced Burgers 1D.ipynb   ->  data_Burgers1D.npz
  0 - Data generation - KDV 1D.ipynb              ->  data_KdV1D.npz
  0 - Data generation - Burgers 2D.ipynb          ->  data_Burgers2D.npz
  1 - Method - EP Bayesian OpInf - Forced Burgers 1D.ipynb  ->  results_Burgers1D.npz
  1 - Method - EP Bayesian OpInf - KDV 1D.ipynb             ->  results_KdV1D.npz
  1 - Method - EP Bayesian OpInf - Burgers 2D.ipynb         ->  results_Burgers2D.npz
```

The three problems are independent of one another. Within a problem, run the `0` notebook first: the `1` notebook reads its `.npz` from the same folder.

## What the notebooks do

**`0 - Data generation`** builds the full-order model and the data the method is given. It discretizes the equation on a uniform periodic grid, checks that the discrete convective operator is energy preserving to machine precision (the $\lambda=2/3$ stencil of the paper), integrates the reference trajectories forthe five training parameter values and the held-out one, samples `M` snapshots per trajectory at random times in the training window, adds `XI` relative Gaussian noise, builds the centred POD basis of dimension `R`, and reports the projection error. It also computes the intrusive Galerkin operators, which the paper uses only as a reference point.

**`1 - Method - EP Bayesian OpInf`** is the method itself. It fits one Gaussian process per reduced coordinate and extracts state estimates, analytic derivative estimates and their precisions; assembles the data matrix; selects the prior scales by maximizing the marginal likelihood; builds the energy-preservation constraint matrix `C`; applies the Kalman update that gives the constrained posterior; draws samples and corrects them, so that every draw satisfies the constraint to machine precision; evaluates the plain and shifted stability certificates and the Clopper–Pearson interval; integrates the certified draws; and prints the error tables and energy diagnostics that appear in section 8. The KdV notebook additionally imposes the skew constraint on the linear part and compares the three posteriors.

## Requirements

Python 3.9 or later, with

```
numpy  scipy  matplotlib  scikit-learn  cvxpy  jupyter
```

`scikit-learn` is used for the Gaussian process regression and `cvxpy` for the semidefinite program behind the shifted certificate; everything else is NumPy and SciPy.