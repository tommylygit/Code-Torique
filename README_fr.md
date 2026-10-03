# Code Torique de Kitaev

Étude mathématique du **code torique de Kitaev** (code correcteur d'erreurs quantique topologique) et **simulation numérique** de la dynamique des anyons sous un canal de Pauli stochastique.

> Projet réalisé à **CY Tech — GM DATA**, juin 2026.

> Superviseur : Garrigue.

> Etudiants : LY Tommy,  Imran El Azri Ennassiri,  Ayman Munglee, Mathis Oudin, Adel Noui
---

## Sommaire

- [Présentation](#présentation)
- [Contenu du dépôt](#contenu-du-dépôt)
- [Résumé du rapport](#résumé-du-rapport)
- [Simulation numérique](#simulation-numérique)
- [Installation et utilisation](#installation-et-utilisation)
- [Limites et pistes d'amélioration](#limites-et-pistes-damélioration)
- [Références](#références)


---

## Présentation

Le code torique encode de l'information quantique non pas dans des qubits isolés, mais dans la **topologie** d'un réseau de qubits plongé sur un tore. L'information logique est portée par des cycles non contractibles du tore : une erreur locale ne peut pas la détruire à moins de traverser tout le réseau.

Ce projet comporte deux volets :

1. **Un rapport théorique** (`rapport_final.pdf`) qui construit le code de façon rigoureuse, de l'espace de Hilbert jusqu'à la résistance exponentielle aux erreurs locales.
2. **Un notebook Python** (`code_torique_simulation.ipynb`) qui simule la dynamique des erreurs sur le syndrome et illustre certains résultats du rapport.

## Contenu du dépôt

```
.
├── rapport_final.pdf                 # Rapport complet (formalisme, topologie, correction d'erreurs)
├── code_torique_simulation.ipynb     # Notebook de simulation
└── README.md
```

## Résumé du rapport

Le rapport se divise en trois parties.

### 1. Formalisme et structure du code

- Espace de Hilbert à $n$ qubits $\mathbb{C}^{2^n}$, matrices de Pauli $X$ et $Z$ (hermitiennes, involutives, anticommutantes).
- Réseau carré $L \times L$ à conditions aux limites périodiques (tore) : $|V| = |P| = L^2$ et $|E| = 2L^2$. Les **qubits sont placés sur les arêtes**, soit $n = 2L^2$.
- Opérateurs stabilisateurs :
  - **étoile** $A_v = \prod_{e \in \partial v} X_e$ (sur les 4 arêtes incidentes à un sommet) ;
  - **plaquette** $B_p = \prod_{e \in \partial p} Z_e$ (sur les 4 arêtes du contour d'une face).
- Démonstration que $[A_v, A_{v'}] = [B_p, B_{p'}] = [A_v, B_p] = 0$ : une étoile et une plaquette partagent 0 ou 2 arêtes, donc les deux signes $-1$ de l'anticommutation s'annulent.
- Lemme spectral (spectre $\subseteq \{-1, +1\}$), codiagonalisation et décomposition de $\mathbb{C}^{2^n}$ en secteurs propres $S_{\mu, w}$ indexés par le syndrome.

### 2. Hamiltonien, espace fondamental et topologie

- Hamiltonien $H = -J\left(\sum_v A_v + \sum_p B_p\right)$, avec $J > 0$.
- Espace fondamental $\mathcal{C}$ : $\dim \mathcal{C} = 2^{n-k} = 4$ **indépendamment de $L$**, avec $k = 2(L^2 - 1)$ stabilisateurs indépendants.
- Lien avec l'homologie du tore : $H_1(\mathbb{T}^2, \mathbb{F}_2) \cong \mathbb{Z}_2^2$. Les cycles non contractibles $\gamma_1, \gamma_2$ définissent les opérateurs logiques $\bar{Z}_1, \bar{Z}_2$ (et leurs duaux $\bar{X}_1, \bar{X}_2$), soit **2 qubits logiques** d'origine purement topologique.

### 3. Robustesse face aux erreurs

- Modèle d'erreur : canal de Pauli indépendant (erreur $X$ avec probabilité $p$ sur chaque arête).
- Une erreur $X_e$ **anticommute** avec les deux plaquettes adjacentes à $e$ et y crée une paire d'**anyons** ($B_p = -1$). Les anyons sont toujours créés par paires : leur nombre est **pair** (Prop. 9.2).
- La mesure du **syndrome** localise les anyons sans détruire l'information logique (les $B_p$ commutent avec $\bar{X}_i, \bar{Z}_i$).
- Décodage par appariement parfait de poids minimal (**MWPM**). La correction réussit si l'erreur composée avec la correction est un bord, et échoue si elle forme un cycle non contractible.
- Distance du code $d = L$ et probabilité d'erreur logique $O(p^{L})$ : décroissance exponentielle avec la taille du réseau. Seuil numérique $p_{\text{seuil}} \approx 10{,}3\,\%$ (Dennis *et al.*, 2002).
- Ouverture : le gap spectral protège dynamiquement le code. Créer un anyon coûte $2J$ (soit $4J$ pour la première paire excitée), et les excitations thermiques sont supprimées par un facteur de Boltzmann $e^{-2J/k_B T}$.

## Simulation numérique

Le notebook implémente un **canal de Pauli stochastique classique** qui agit directement sur le **syndrome**, c'est-à-dire la grille $L \times L$ des valeurs propres $B_p \in \{-1, +1\}$.

**Principe.** À chaque pas de temps, chaque arête subit indépendamment une erreur $X$ avec probabilité $p$. Une erreur sur une arête retourne le signe des deux plaquettes adjacentes, ce qui crée ou annihile des anyons par paires. Les arêtes horizontales et verticales sont indexées respectivement par `i*L + j` et `L**2 + i*L + j`.

**Organisation du notebook :**

| Partie | Contenu |
|---|---|
| 1. Modèle et fonctions de base | Structure du réseau, `adjacent_plaquettes`, `step`, `n_anyons`, `simulate`, vérification de la parité des anyons (Prop. 9.2) |
| 2. Régime sous-critique | Animation du syndrome ($L = 30$, $p = 0{,}01$, 300 pas) et courbe du nombre d'anyons |
| 3. Densité d'anyons vs $p$ | Balayage de $p \in [0{,}01 ; 0{,}25]$ pour $L \in \{10, 20, 30\}$ |
| 4. Comparaison de régimes | Instantanés du syndrome pour $p = 0{,}01$, $0{,}103$ et $0{,}20$ ($L = 40$) |

**Ce que la simulation illustre :**

- le retournement de deux plaquettes adjacentes par une erreur $X_e$ (Prop. 9.1) ;
- la **conservation de la parité** du nombre d'anyons, vérifiée numériquement (Prop. 9.2) ;
- la lecture du syndrome comme grille d'excitations.

## Installation et utilisation

**Prérequis :** Python 3.9+

```bash
git clone https://github.com/164Imran/Code-Toric.git
cd Code-Toric
pip install numpy matplotlib tqdm jupyter
jupyter notebook code_torique_simulation.ipynb
```

Exécuter les cellules dans l'ordre. Quelques remarques pratiques :

- Les fonctions de `step` sont écrites avec des boucles Python : le balayage de la partie 3 (25 valeurs de $p$ × 3 tailles × 300 pas) peut prendre plusieurs minutes. Réduire `p_values`, `L_values` ou `n_meas` pour accélérer.
- L'animation de la partie 2 s'affiche avec `plt.show()`. Pour l'exporter, décommenter la ligne `ani.save('toric_code.gif', writer='pillow', fps=25)`.
- Fixer `np.random.seed(...)` en début de notebook pour obtenir des résultats reproductibles.

## Limites et pistes d'amélioration

Le modèle simulé n'applique **aucune correction** : le syndrome évolue librement sous le bruit, sans décodeur. Cela a une conséquence importante sur l'interprétation des résultats.

- Chaque plaquette est retournée par ses 4 arêtes voisines, donc son signe se comporte comme une marche aléatoire sur $\{-1, +1\}$. Le syndrome tend vers un état aléatoire et la **densité d'anyons converge vers 1/2**, quelle que soit la valeur de $p$ (observé pour $p$ de $0{,}01$ à $0{,}20$ avec $L = 10$ et $L = 20$). Le temps pour y parvenir diminue quand $p$ augmente.
- La courbe « densité vs $p$ » de la partie 3 est donc quasi plate à la longueur d'équilibration utilisée, et ne fait pas apparaître la transition au voisinage de $p_{\text{seuil}} \approx 0{,}103$. Ce seuil est un résultat théorique et numérique de la littérature (Dennis *et al.*), pas une propriété mesurée ici.

Pour reproduire le seuil, il faudrait simuler le **code capacity model** complet :

1. tirer une erreur $X$ i.i.d. de probabilité $p$ sur les $2L^2$ arêtes ;
2. calculer le syndrome (anyons sur les plaquettes) ;
3. décoder par MWPM (par exemple avec [PyMatching](https://github.com/oscarhiggott/PyMatching)) ;
4. tester si l'erreur composée avec la correction est un cycle non contractible (erreur logique) ;
5. tracer le **taux d'erreur logique en fonction de $p$** pour plusieurs $L$ : les courbes se croisent au seuil.

## Références

1. A. R. Calderbank, P. W. Shor. *Good quantum error-correcting codes exist.* Physical Review A, 54(2):1098–1105, 1996.
2. E. Dennis, A. Kitaev, A. Landahl, J. Preskill. *Topological quantum memory.* Journal of Mathematical Physics, 43(9):4452–4505, 2002.
3. J. Edmonds. *Paths, trees, and flowers.* Canadian Journal of Mathematics, 17:449–467, 1965.
4. A. Yu. Kitaev. *Fault-tolerant quantum computation by anyons.* Annals of Physics, 303(1):2–30, 2003.
5. A. M. Steane. *Multiple-particle interference and quantum error correction.* Proceedings of the Royal Society A, 452(1954):2551–2577, 1996.
6. A. Wang. *The toric code.* REU Paper, University of Chicago, 2024.
