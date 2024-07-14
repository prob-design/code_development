# Code Development

This repository is meant to be a temporary collection of code, before it is moved to another location. It is not meant to be shared or re-used in any way. Contact rlanzafame if needed.

## Instructions for Siemen, July, 2024

Given: _two random variables $X_1$ and $X_2$, each of which has a marginal distribution $F_{X_1}$ and $F_{X_2}$ and a dependence structure defined by a Copula with parameter ??=?? (parameter(s) depends on Copula)._

We are then able to:
- write out mathematicall as much of the bivariate distribution $F_{X_1,X_2}$ and related objects as possible
- have code that uses nothing more than `scipy.stats` and `numpy` to make relevant calculations and produce figures

### General instructions

1. Read through this README
2. Quickly review the existing code in this repo, and quickly take a look at Aas 2009 paper; don't spend more than a few min with the paper, just note the main text pg 183 where it defines copulas and h functions, then note Appendix C which defines some of the analytic copulas
3. Start by implementing the Gaussian Copula (see notebook, it's mostly done). Create a new notebook where you implement it and show that it is done correctly (see template below). Start with the Copula alone, then add the marginal distributions.
4. Have Robert check the Gaussian case, then we can move on to the non-Gaussian copulas. If you get stuck, you can at least write out the math and Python functions for the Copulas and make computations and figures with them in the notebooks to show they are implemented properly.

### Template Notebook

1. Write out the equation for the Copula, then implement all necessary aspects in Python functions.
2. Include following calculations and figures:
   1. Set Copula parameter (i.e., for Gaussian, its $\rho$)
   2. evaluate the copula for u and v
   3. make the same calc with pyvinecopulib and confirm that they give the same results (PDF and CDF)
   4. plot the density contours of the copula (check visually with [Max' website](https://www.maxramgraber.com/copula-sandbox) that the contours look ok). *evaluate the PDF, don't create the contours from a sample!*
   5. create the same plots using pyvinecopulib and confirm visually that they are the same
   6. create a sample from the copula and add it to the contour plot; verify that it looks ok. Repeat with the pyvinecopulib version
3. Use `data.csv` as a case study for $X_1$ and $X_2$
   1. define marginals (for simplicity, you can start with Gaussian marginals)
   2. do everything above with X1 and X2
   3. for a given X1 and X2, find u and v; do the same in reverse. Check that the same values are provided by the pyvinecopulib versions of the distributions

**Note:** for the Gaussian copula you can check the case of Gaussian marginals with the multivariate normal from `scipy.stats` instead of `pyvinecopulib`.

## Copula

Main goal: define bivariate distributions of dependent random variables $X_1$ and $X_2$ in math and in code. _In code_ means that we can implement the following:
- define the marginal distributions with univariate distributions and dependence structure with Copulas
- evaluate the CDF ($F_{X_1,X_2}(x_1,x_2)$) for any value of $X_1$ and $X_2$
- sample from the distribution (i.e., return $N$ samples of $X_1$ and $X_2$)
- create a contour plot of the PDF ($f_{X_1,X_2}(x_1,x_2)$)
- evaluate the Copula in a similar way as described above (i.e., probabilities and contours from the CDF and PDF of $u$ and $v$ on the standard uniform space)
- be able to choose values of two random variables in the $X_1,X_2$ space and see the equivalent information in the $u,v$ space and vice-versa
- where possible, evaluate the conditional distributions of the random variables: $F_{X_1|X_2}$ and vice-versa (related to h functions in the Aas paper)

Some more details...make sure this repo contains:
- write out mathematical representations of the things that are implemented in the code
- have Python implementations of several bivariate copulas (Gaussian, Clayton, Gumbel, Frank, Student-t)
- avoid the use of `pyvinecopulib` wherever possible, preferring Python implementations using only `scipy` and `numpy`
- use `pyvinecopulib` to verify the implementation of the Copulas using assert statements (should be able to do this with Gaussian with only `scipy.stats`)
- can also verify the implementation from the solution for MUDE Project 4, as well as use it as an example for how `pyvinecopulib` was used to construct the multivariate distribution for a non-Gaussian copula and non-Gaussian marginals

## Previous work

See `copula_dev.ipynb`, which was written in Fall 2023. It is:
- a nearly-completed implementation of the bivariate Gaussian copula (the numerical results were not quite right, so this needs to be fixed and tested)
- was developed when preparing [Project 4 for MUDE, Fall 2023](https://mude.citg.tudelft.nl/course-files/Project_4/) (the second part, the stuff from Week 8)

## A hint at the math

Assume we have two random variables, $X_1$ and $X_2$, each with non-Gaussian marginal distributions. They are _not_ statistically independent. We wish to define the multivariate (bivariate) probability distribution for these two random variables. Mathematically, this means we need either the PDF ($f_{X_1,X_2}(x_1,x_2)$) or CDF ($F_{X_1,X_2}(x_1,x_2)$) to describe it completely.

This is a complete mathematical definition since (in theory) we can differentiate or integrate one to get the other. Of course, implementing this in code without extensive numerical approximations is not trivial (we will see how far we can get with this for the analytic copulas available).

The CDF is simply:
$$
F_{X_1,X_2}(x_1,x_2) = C\left(F_{X_1}(x_1),F_{X_2}(x_2)\right) = C(u,v)
$$
Where $C$ is a Copula and the random vector $(u,v)$ are both the arguments of the Copula and density functions and represent the coordinates of $(X_1,X_2)$ transformed to the uniform square:
$$ 
(u,v) \in [0,1]^2 \qquad (X_1,X_2) \in \mathbb{R}^2
$$

## How to sample?

1. Sample from $C$ to get realizations $(u, v)$ (method `multivariate_normal.rvs`)
2. Transform $(u, v)\rightarrow(X_1, X_2)$ using $X_1 = F_1^{-1}(u)$ and $X_2 = F_i^{-1}(v)$
