# Antithetic-Estimators
This is my implementation of the [paper](https://link.springer.com/article/10.1007/s11222-019-09859-z) on *Antithetic and Monte-Carlo Kernel Estimators for Partial Rankings*

### Summary

This paper is a fascinating application of kernel methods to the permutation spaces. So the distances aren't Euclidean, but measuring how different *rankings* are. 

### Motivation

Many kernel functions capture interesting "distances" between rankings of a set of objects (e.g. number of discordant pairs between 2 permutations). However, when applying these to *partial rankings*, things get tricky. The number of full rankings that are candidates for each partial ranking can be huge, and we need to compute the pairwise distances of all possible full ranking pairs to get the marginal equivalent for 2 pairs of partial rankings.

This results in combinatorial explosion, and we need an estimator for this marginalized kernel to keep things tractable.

### Solution

The authors propose Monte-Carlo sampling to select full rankings for each pair of partial rankings, and using those to estimate the marginal equivalent of the kernel function.

Furthermore, they suggest a tweak where for each Monte-Carlo sample, we also pick its "most distant-relative" (i.e., the full ranking compliant with the same partial ranking, but which is "farthest" from the MC sample in this space). This gives us an estimator with provably lower variance.

---

### Paper Sections

- Section 2: examples of semimetrics (in particular, *negative-type semimetrics* which can be constructed from any Mercer kernel) and kernel functions for permutation spaces
- Section 3: partial rankings, marginalized kernels, Monte-Carlo estimators for these and their variance
- Section 4: selecting samples with antithetic variates, and demonstrating the estimator has a lower variance
- Section 5: experiments using Agglomerative Clustering, MMD, Gaussian Process classifier

---

### Implementation

I followed the formula and implemented the estimators using first the Mallows Kernel and then the Kendal Kernel functions.

I used the [SUSHI preference dataset](http://www.kamishima.net/sushi/) to get a full Gram matrix and semimetric matrix for partial rankings. This was done for 100 users, with MC sample size of 20, and partial rankings obtained by masking the bottom 6 ranks for each respondent.

I then used these distance matrices as inputs to the Sci-kit Learn Agglomerative Clustering method to compare tree purities.

### Results

Tree purity for 
- Full rankings: 0.55
- MC estimator (Mallows kernel): 0.50
- Antithetic version (Mallows kernel): 0.50

The tree purities are similar to what is obtained using the full ranking data, so this is consistent with the paper's results. However, the tree purities are somewhat different from what the authors got. 

### Possible Explanations

1. The 100 samples chosen from the dataset of 5000 examples made a big difference. This is quite possible, if the clusters are skewed.
2. The *median distance criterion* used by the authors for exponential kernels was different. There appears to be **no consensus** around what this criterion means. I used the formula empirically recommended by [this paper](https://arxiv.org/pdf/1708.05106.pdf) (page 3).
3. Or quite simply, I coded the negative-type semimetric different from how the authors intended.

---

### Future Directions to Extend This Work

- Lattice kernels
- Kemeny consensus: [link](https://pastel.archives-ouvertes.fr/tel-01983274/)
- Latin hypercube sampling: [link](https://projecteuclid.org/euclid.ejs/1580202025)
- Geometrically coupled sampling: [link](https://proceedings.neurips.cc/paper/2018/hash/b3e3e393c77e35a4a3f3cbd1e429b5dc-Abstract.html)
