# Démineur avec un agent basé sur la connaissance

Jeu de Démineur sur une grille de 8 × 8, où le joueur peut révéler les cases ou demander à un agent de jouer. L'agent utilise une base de connaissances et des règles d'inférence logique pour identifier les cases sûres et les mines.

## Interface du runner

![Interface du Démineur avec les boutons AI Move et Reset](assets/images/runner-interface.png)

## Fonctionnalités

- Plateau de **8 × 8 cases** contenant **8 mines**.
- Révélation manuelle des cases et signalement des mines avec des drapeaux.
- Coups proposés par l'agent à partir des informations révélées.
- Détection de victoire ou de défaite.
- Possibilité de recommencer une partie avec un nouveau plateau.

## Comment jouer

1. Lancez le jeu et cliquez sur **Play Game**.
2. Cliquez sur une case pour la révéler.
3. Faites un clic droit sur une case non révélée pour ajouter ou retirer un drapeau.
4. Cliquez sur **AI Move** pour demander à l'agent de jouer.
5. Cliquez sur **Reset** pour commencer une nouvelle partie.

La partie est gagnée lorsque les drapeaux placés correspondent exactement à toutes les mines. Révéler une mine met fin à la partie et affiche les mines du plateau.

## Algorithme de l'IA

L'agent utilise une **base de connaissances en logique propositionnelle**. Chaque case peut être représentée par une proposition : `M_A` signifie « la case A contient une mine ». Cette proposition est vraie si A est une mine et fausse si A est sûre. Les nombres révélés fournissent des faits que l'agent combine pour déduire le statut de cases inconnues.

### Représentation des connaissances

Chaque nombre révélé indique combien de mines se trouvent dans les cases voisines encore cachées. Les cases voisines sont les cases adjacentes horizontalement, verticalement ou en diagonale (jusqu'à huit, sans compter la case révélée). L'agent mémorise l'indice sous forme d'une contrainte compacte :

```text
{A, B, C} = 1
```

Cela signifie qu'exactement une des cases A, B ou C est une mine, autrement dit exactement une des propositions `M_A`, `M_B` et `M_C` est vraie. En logique propositionnelle, on peut l'écrire :

```text
(M_A ∨ M_B ∨ M_C)
∧ ¬(M_A ∧ M_B)
∧ ¬(M_A ∧ M_C)
∧ ¬(M_B ∧ M_C)
```

`∨` signifie « ou », `∧` signifie « et » et `¬` signifie « non ». Le premier terme dit qu'il y a au moins une mine ; les autres empêchent qu'il y en ait deux. Le programme conserve cette information sous la forme plus courte `{A, B, C} = 1`, plutôt que de stocker toute la formule développée. Si une case de la contrainte est ensuite reconnue sûre, l'agent la retire. S'il découvre que c'est une mine, il la retire et diminue aussi le nombre de mines restant à trouver.

### Déductions logiques

L'agent applique des règles d'inférence pour obtenir de nouveaux faits :

- `{A, B, C} = 0` donne `¬M_A ∧ ¬M_B ∧ ¬M_C` : les trois cases sont sûres ;
- `{A, B} = 2` donne `M_A ∧ M_B` : les deux cases sont des mines.

### Exemple de déduction avec `∧`

Supposons que l'agent ait ces deux informations :

```text
{A, B} = 1
{A, B, C} = 2
```

La première dit qu'exactement une de A et B est une mine :

```text
(M_A ∨ M_B) ∧ ¬(M_A ∧ M_B)
```

La seconde signifie qu'exactement deux des trois cases sont minées. Les possibilités sont :

```text
(M_A ∧ M_B ∧ ¬M_C)
∨ (M_A ∧ ¬M_B ∧ M_C)
∨ (¬M_A ∧ M_B ∧ M_C)
```

La première possibilité est impossible, car elle rendrait A et B toutes les deux minées, ce que la première information interdit. Il ne reste que les deux dernières possibilités, et elles contiennent toutes les deux `M_C`. On peut donc conclure :

```text
({A, B} = 1) ∧ ({A, B, C} = 2)  ⇒  M_C
```

Ici, `∧` combine les informations connues et `⇒` indique la conclusion qu'on peut en tirer. L'agent répète ce type de déduction après chaque nouvelle information, jusqu'à ne plus pouvoir en tirer de nouvelles.

Dans le code, cette conclusion est obtenue en soustrayant la première contrainte de la seconde : il reste `{C} = 1`.

### Choix d'un coup et limites

Quand on clique sur **AI Move**, l'agent choisit au hasard parmi les cases qu'il sait sûres et qu'il n'a pas encore jouées. S'il n'en connaît aucune, il choisit au hasard une case non jouée qu'il ne sait pas être une mine. Ce second choix n'est pas garanti sûr : l'agent ne calcule pas la probabilité de chaque case et peut donc perdre. S'il ne reste aucune case possible, il n'effectue pas de coup.

## Installation et lancement

```bash
git clone https://github.com/cestbryan-ng/KBA-Minesweeper.git
cd KBA-Minesweeper
python -m pip install -r requirements.txt
python runner.py
```
