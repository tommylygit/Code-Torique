# Kitaev's Toric Code

A mathematical study of **Kitaev's toric code** (a topological quantum error-correcting code) together with a **numerical simulation** of anyon dynamics under a stochastic Pauli channel.

> Project carried out at **CY Tech — DATA SCIENCE GROUP**, June 2026.

> Supervisor: Garrigue

> Students : Tommy LY,  Imran El Azri Ennassiri, Ayman Munglee, Mathis Oudin, Adel Noui




*The report (`rapport_final.pdf`) is written in French. This README is also available in French: [README.fr.md](README.fr.md).*

---

## Table of contents

- [Overview](#overview)
- [Repository contents](#repository-contents)
- [Report summary](#report-summary)
- [Numerical simulation](#numerical-simulation)
- [Installation and usage](#installation-and-usage)
- [Limitations and possible improvements](#limitations-and-possible-improvements)
- [References](#references)
- [Authors](#authors)

---

## Overview

The toric code stores quantum information not in individual qubits but in the **topology** of a lattice of qubits embedded on a torus. Logical information is carried by non-contractible cycles of the torus: a local error cannot destroy it unless it spans the whole lattice.

This project has two parts:

1. **A theoretical report** (`rapport_final.pdf`) that builds the code rigorously, from the Hilbert space up to exponential suppression of local errors.
2. **A Python notebook** (`code_torique_simulation.ipynb`) that simulates error dynamics on the syndrome and illustrates some of the report's results.

## Repository contents

```
.
├── rapport_final.pdf                 # Full report (formalism, topology, error correction), in French
├── code_torique_simulation.ipynb     # Simulation notebook
├── README.md                         # English README
└── README.fr.md                      # French README
```

## Report summary

The report is divided into three parts.

### 1. Formalism and structure of the code

- Hilbert space of $n$ qubits $\mathbb{C}^{2^n}$, Pauli matrices $X$ and $Z$ (Hermitian, involutive, anticommuting).
- Square $L \times L$ lattice with periodic boundary conditions (torus): $|V| = |P| = L^2$ and $|E| = 2L^2$. **Qubits sit on the edges**, so $n = 2L^2$.
- Stabilizer operators:
  - **star** $A_v = \prod_{e \in \partial v} X_e$ (on the 4 edges incident to a vertex);
  - **plaquette** $B_p = \prod_{e \in \partial p} Z_e$ (on the 4 edges bounding a face).
- Proof that $[A_v, A_{v'}] = [B_p, B_{p'}] = [A_v, B_p] = 0$: a star and a plaquette share either 0 or 2 edges, so the two minus signs from anticommutation cancel.
- Spectral lemma (spectrum $\subseteq \{-1, +1\}$), simultaneous diagonalization, and decomposition of $\mathbb{C}^{2^n}$ into eigenspaces $S_{\mu, w}$ labelled by the syndrome.

### 2. Hamiltonian, ground space and topology

- Hamiltonian $H = -J\left(\sum_v A_v + \sum_p B_p\right)$, with $J > 0$.
- Ground space $\mathcal{C}$: $\dim \mathcal{C} = 2^{n-k} = 4$ **independently of $L$**, with $k = 2(L^2 - 1)$ independent stabilizers.
- Link with the homology of the torus: $H_1(\mathbb{T}^2, \mathbb{F}_2) \cong \mathbb{Z}_2^2$. The non-contractible cycles $\gamma_1, \gamma_2$ define the logical operators $\bar{Z}_1, \bar{Z}_2$ (and their duals $\bar{X}_1, \bar{X}_2$), giving **2 logical qubits** of purely topological origin.

### 3. Robustness against errors

- Error model: independent Pauli channel (an $X$ error with probability $p$ on each edge).
- An error $X_e$ **anticommutes** with the two plaquettes adjacent to $e$ and creates a pair of **anyons** there ($B_p = -1$). Anyons are always created in pairs, so their number is **even** (Prop. 9.2).
- Measuring the **syndrome** locates the anyons without destroying the logical information (the $B_p$ commute with $\bar{X}_i, \bar{Z}_i$).
- Decoding by minimum-weight perfect matching (**MWPM**). Correction succeeds if the error composed with the correction is a boundary, and fails if it forms a non-contractible cycle.
- Code distance $d = L$ and logical error probability $O(p^{L})$: exponential decay with lattice size. Numerical threshold $p_{\text{threshold}} \approx 10.3\,\%$ (Dennis *et al.*, 2002).
- Outlook: the spectral gap provides dynamical protection. Creating an anyon costs $2J$ (i.e. $4J$ for the first excited pair), and thermal excitations are suppressed by a Boltzmann factor $e^{-2J/k_B T}$.

## Numerical simulation

The notebook implements a **classical stochastic Pauli channel** acting directly on the **syndrome**, i.e. the $L \times L$ grid of eigenvalues $B_p \in \{-1, +1\}$.

**Principle.** At each time step, every edge independently suffers an $X$ error with probability $p$. An error on an edge flips the sign of the two adjacent plaquettes, which creates or annihilates anyons in pairs. Horizontal and vertical edges are indexed by `i*L + j` and `L**2 + i*L + j` respectively.

**Notebook outline:**

| Part | Content |
|---|---|
| 1. Model and basic functions | Lattice structure, `adjacent_plaquettes`, `step`, `n_anyons`, `simulate`, anyon parity check (Prop. 9.2) |
| 2. Subcritical regime | Syndrome animation ($L = 30$, $p = 0.01$, 300 steps) and anyon-count curve |
| 3. Anyon density vs $p$ | Sweep over $p \in [0.01, 0.25]$ for $L \in \{10, 20, 30\}$ |
| 4. Regime comparison | Syndrome snapshots for $p = 0.01$, $0.103$ and $0.20$ ($L = 40$) |

**What the simulation illustrates:**

- the flipping of two adjacent plaquettes by an $X_e$ error (Prop. 9.1);
- **conservation of anyon parity**, checked numerically (Prop. 9.2);
- reading the syndrome as a grid of excitations.

## Installation and usage

**Requirements:** Python 3.9+

```bash
git clone https://github.com/164Imran/Code-Toric.git
cd Code-Toric
pip install numpy matplotlib tqdm jupyter
jupyter notebook code_torique_simulation.ipynb
```

Run the cells in order. A few practical notes:

- The `step` function uses plain Python loops: the sweep in part 3 (25 values of $p$ × 3 sizes × 300 steps) can take several minutes. Reduce `p_values`, `L_values` or `n_meas` to speed it up.
- The animation in part 2 is displayed with `plt.show()`. To export it, uncomment the line `ani.save('toric_code.gif', writer='pillow', fps=25)`.
- Set `np.random.seed(...)` at the top of the notebook for reproducible results.

## Limitations and possible improvements

The simulated model applies **no correction**: the syndrome evolves freely under noise, without a decoder. This has an important consequence for interpreting the results.

- Each plaquette is flipped by its 4 neighbouring edges, so its sign behaves like a random walk on $\{-1, +1\}$. The syndrome drifts toward a random state and the **anyon density converges to 1/2**, whatever the value of $p$ (observed for $p$ from $0.01$ to $0.20$ with $L = 10$ and $L = 20$). The time needed to get there decreases as $p$ increases.
- The "density vs $p$" curve in part 3 is therefore nearly flat at the equilibration length used, and does not show a transition near $p_{\text{threshold}} \approx 0.103$. This threshold is a theoretical and numerical result from the literature (Dennis *et al.*), not a property measured here.

To reproduce the threshold, one would need to simulate the full **code capacity model**:

1. draw i.i.d. $X$ errors with probability $p$ on the $2L^2$ edges;
2. compute the syndrome (anyons on plaquettes);
3. decode with MWPM (for example using [PyMatching](https://github.com/oscarhiggott/PyMatching));
4. check whether the error composed with the correction is a non-contractible cycle (logical error);
5. plot the **logical error rate as a function of $p$** for several $L$: the curves cross at the threshold.

## References

1. A. R. Calderbank, P. W. Shor. *Good quantum error-correcting codes exist.* Physical Review A, 54(2):1098–1105, 1996.
2. E. Dennis, A. Kitaev, A. Landahl, J. Preskill. *Topological quantum memory.* Journal of Mathematical Physics, 43(9):4452–4505, 2002.
3. J. Edmonds. *Paths, trees, and flowers.* Canadian Journal of Mathematics, 17:449–467, 1965.
4. A. Yu. Kitaev. *Fault-tolerant quantum computation by anyons.* Annals of Physics, 303(1):2–30, 2003.
5. A. M. Steane. *Multiple-particle interference and quantum error correction.* Proceedings of the Royal Society A, 452(1954):2551–2577, 1996.
6. A. Wang. *The toric code.* REU Paper, University of Chicago, 2024.


