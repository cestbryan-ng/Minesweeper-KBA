# Démineur avec un agent basé sur la connaissance

Ce projet propose une version graphique du Démineur et un agent qui déduit les cases sûres et les mines à partir des informations révélées sur le plateau. L'agent s'appuie sur une base de connaissances et des règles d'inférence logique.

## Interface du runner

![Interface graphique du Démineur avec les boutons AI Move et Reset](assets/images/runner-interface.png)

## Fonctionnalités

- Plateau de **8 × 8 cases** contenant **8 mines**.
- Révélation manuelle des cases ou demande d'un coup à l'IA.
- Signalement manuel des mines avec des drapeaux.
- Inférence des cases sûres et des mines à partir des nombres révélés.
- Réinitialisation de la partie.

## Comment fonctionne l'IA ?

Chaque nombre révélé permet de créer une phrase logique de la forme « parmi ces cases voisines, `n` sont des mines ». L'agent conserve ces phrases dans sa base de connaissances, marque les mines et les cases sûres lorsqu'elles peuvent être déduites, puis compare les phrases entre elles pour en tirer de nouvelles conclusions.

Lorsqu'on demande un coup à l'IA, elle choisit d'abord une case qu'elle sait sûre. Si aucune case sûre n'est connue, elle choisit au hasard une case qui n'a pas encore été jouée et qui n'est pas identifiée comme une mine. Ce choix aléatoire peut tomber sur une mine : l'agent ne garantit donc pas une partie gagnante.

## Installation

Depuis le dossier du projet :

```bash
python -m pip install -r requirements.txt
```

La dépendance graphique utilisée est Pygame.

## Lancer le jeu

Depuis le dossier du projet, où se trouvent également les dossiers `assets/` :

```bash
python runner.py
```

## Commandes

- **Clic gauche sur une case** : la révéler.
- **Clic droit sur une case non révélée** : ajouter ou retirer un drapeau.
- **AI Move** : demander à l'agent de jouer un coup.
- **Reset** : démarrer une nouvelle partie avec un plateau et une position des mines aléatoires.

La partie est gagnée lorsque les drapeaux placés correspondent exactement à toutes les mines. Révéler une mine met fin à la partie et affiche les mines du plateau.

## Structure du projet

- `minesweeper.py` : logique du jeu, phrases logiques et agent de résolution.
- `runner.py` : interface graphique Pygame et interactions.
- `assets/fonts/` : police utilisée par l'interface.
- `assets/images/` : icônes de mine et de drapeau.
- `requirements.txt` : dépendances Python.

## Dépôt

```bash
git clone https://github.com/cestbryan-ng/KBA-Minesweeper.git
cd KBA-Minesweeper
python -m pip install -r requirements.txt
python runner.py
```
