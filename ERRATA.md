# Erratum — Kitaev's Toric Code report / rapport « Code Torique de Kitaev »

*English summary first, French version below.*

## English summary

The report (`rapport_final.pdf`) was submitted in **June 2026** and is kept **unchanged**. This file lists corrections identified **after submission** (October 2026); where the PDF and this repository differ, the notebook, READMEs and this erratum prevail.

1. **Simulation section.** The report states that the simulation exhibits a phase transition near $p_{\text{threshold}} \approx 0.103$. It does not: the model has no decoder, so the anyon density relaxes to $1/2$ for every $p$, following $\rho(t) = \tfrac{1}{2}[1-(1-2p)^{4t}]$. The threshold is a literature result (Dennis *et al.*, 2002), not measured here. Measuring it would require the full code capacity model with MWPM decoding.
2. **Proposition 13.1.** The statement gives a gap $\Delta = 2J$ but the proof yields $\Delta = 4J$ (i.e. $2J$ per anyon). The correct gap is $4J$.
3. **Notebook.** Revised in October 2026 (text aligned with the results, corrupted LaTeX labels fixed, theory curve added, fixed random seed added, English descriptions added alongside the French ones). The simulation functions (`step`, `simulate`, etc.) are unchanged; Git history keeps the original version.

---

## Version française

Le rapport (`rapport_final.pdf`) a été déposé en **juin 2026** et est conservé **tel quel**, sans modification. Ce fichier recense les corrections identifiées **après le dépôt** (octobre 2026). Lorsque le PDF et ce dépôt diffèrent, le notebook, les README et cet erratum font foi.

### 1. Section « Simulation numérique » (dernière page du rapport)

**Ce que dit le rapport.** La simulation « fait apparaître la transition de phase au voisinage du seuil $p_{\text{seuil}} \approx 0{,}103$ » et constitue « une illustration directe » du Théorème 12.1 et de la Proposition 11.1.

**Correction.** Le modèle simulé n'applique aucune correction (pas de décodeur). Chaque plaquette est retournée par ses quatre arêtes voisines, donc

$$\mathbb{E}[B_p(t)] = B_p(0)\,(1-2p)^{4t}.$$

À partir de l'état fondamental, la densité d'anyons vaut

$$\rho(t) = \tfrac{1}{2}\left[1 - (1-2p)^{4t}\right] \xrightarrow[t\to\infty]{} \tfrac{1}{2},$$

quelle que soit la valeur de $p$ (temps de relaxation $\approx 1/(8p)$ pas). La courbe « densité vs $p$ » est donc quasi plate (valeurs entre 0,49 et 0,50 pour $L \in \{10, 20, 30\}$ et $p \in [0{,}01\,;\,0{,}20]$) et **ne fait pas apparaître de transition**.

Le seuil $p_{\text{seuil}} \approx 0{,}103$ est un résultat de la littérature (Dennis *et al.*, 2002), **non mesuré** par cette simulation. De même, la simulation n'illustre ni la distance $d = L$ ni la décroissance exponentielle de l'erreur logique (Théorème 12.1). Pour les mesurer, il faudrait simuler le *code capacity model* avec décodage MWPM (Exemple 13.1 du rapport).

Ce que la simulation illustre bien : le retournement de deux plaquettes adjacentes par une erreur $X_e$ (Prop. 9.1), la conservation de la parité des anyons (Prop. 9.2), et la relaxation de la densité d'anyons vers $1/2$ en l'absence de correction.

### 2. Proposition 13.1 (section 13.1)

**Ce que dit le rapport.** L'énoncé affirme un gap $\Delta = 2J$, alors que la démonstration conclut $\Delta = 4J$ pour le premier niveau excité (deux plaquettes excitées).

**Correction.** Le gap vaut $\Delta = 4J$, soit $2J$ par anyon (les anyons sont créés par paires, Prop. 9.2). Le facteur de Boltzmann par anyon, $e^{-2J/(k_B T)}$, est inchangé.

### 3. Notebook

Le notebook `code_torique_simulation.ipynb` a été révisé en octobre 2026 : les textes qui annonçaient une transition de phase ont été corrigés, des légendes LaTeX corrompues ont été réparées, la courbe théorique $\rho(t)$ a été ajoutée, une graine aléatoire fixe a été introduite et des descriptions en anglais ont été ajoutées à côté des descriptions françaises. Les fonctions de simulation (`step`, `simulate`, etc.) n'ont pas été modifiées. L'historique Git conserve la version d'origine.
