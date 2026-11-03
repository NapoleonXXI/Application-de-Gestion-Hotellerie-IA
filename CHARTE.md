# Charte de design

Cette charte est organisée en deux niveaux. Le niveau 1 est le socle commun de toutes les applications. Chaque projet dispose ensuite d'une charte de niveau 2 qui hérite du niveau 1 et ne précise que ses spécificités.

# Niveau 1 : Application esthétique, simple et fonctionnelle

## 1. Credo

« La simplicité est la sophistication ultime » (Léonard de Vinci).

Le design mêle l'utile et l'agréable. L'utilisateur passe peu de temps dans l'application : il doit trouver simplement chaque bouton et chaque fonction, sans se perdre dans trop de couleurs, de typographies ou de tâches. Exprimer le complexe par la simplicité, c'est ce qui fait la beauté.

## 2. Principes

- Seulement ce qui est utile à l'écran : ce qui ne sert pas la tâche n'apparaît pas.
- Pas de défilement inutile : la vue est condensée et tient à l'écran.
- Séparateurs gris fins de 1 px, toujours derrière le contenu, jamais devant.
- Aucun indicateur superflu : pas de badge, d'icône ou d'animation sans fonction.
- Tout élément nouveau ou particulier est dessiné en accord avec ces règles, sans extravagance non justifiée.

## 3. Règle : aucun élément visuel non demandé

Aucun élément visuel ne s'ajoute sans demande explicite. Exemple à ne pas reproduire : la case bleue apparue sur l'arborescence. Toute nouveauté visuelle est proposée à part et validée avant d'entrer dans une version validée.

## 4. Couleurs

La base est faite de blanc, de gris et d'encre. Le bleu, comme toute autre couleur, peut être employé lorsqu'un usage est demandé et justifié.

| Rôle | Clair | Sombre |
|---|---|---|
| Fond | #FFFFFF | #121212 |
| Encre (texte principal) | #1A1A1A | #EDEDED |
| Texte secondaire | #8A8A8A | #ABABAB |
| Bordure | #E4E4E4 | #333333 |
| Remplissage léger | #F0F0F0 | #262626 |
| Séparateurs de lignes | #EFEFEF | |
| Séparateurs de colonnes | #ECECEC | |
| Gris de sélection intermédiaire | #DCDCDC | |
| Ombres | rgba(0,0,0,0.08) à 0.14 | |

## 5. Typographie

- Famille : Inter, puis -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif.
- Graisses : 400 (texte courant), 500 (libellés, titres), 600 (élément courant, accent).
- Tailles : 16 à 16,5 px (titres), 15 px (boutons, lignes de détail), 14 px (cases, segments), 13 px (libellés de lignes, sous-titres), 12 px (en-têtes, fil d'Ariane, notes), 11 px (indications), 9 px (mentions minimes).
- Chiffres tabulaires (font-variant-numeric: tabular-nums) dans les tableaux.

## 6. Espacements

- Marge de page : 16 px.
- Écart entre éléments : 8 à 10 px ; entre lignes de cases : 18 px.
- Cases et boutons : padding 10 px vertical, 14 px horizontal.
- Cellules : padding 8 à 10 px ; hauteur de ligne 44 px.
- Cible tactile minimale : 44 px.
- Rayons : 6 px (barres), 8 px (cases, boutons), 10 px (cartes), 12 px (panneaux flottants).

## 7. Composants

- Cases : bordure 1 px #E4E4E4, fond transparent, rayon 8 px. Même hauteur sur une ligne, largeur ajustée au texte (120 à 260 px). Aucun trait de liaison.
- Boutons : bordure 1 px #E4E4E4, rayon 8 px, fond transparent, texte encre 15 px, hauteur 44 px.
- Contrôle segmenté : fond #F0F0F0, rayon 9 px ; segment actif fond blanc, texte encre, ombre 0 1px 2px rgba(0,0,0,0.12).
- Panneau flottant : fond blanc, bordure #E4E4E4, rayon 12 px, ombre 0 6px 20px rgba(0,0,0,0.08).

## 8. États

- Survol et appui : fond #F0F0F0, sans couleur vive.
- Sélectionné : fond encre #1A1A1A, texte blanc ; intervalle #DCDCDC.
- Désactivé : texte secondaire, sans action.
- Focus clavier : discret, visible uniquement au clavier via :focus-visible (contour 1 px #D6D6D6, décalage 1 px). Pas de focus programmatique sur les titres.
- Mouvement : transitions courtes (0,18 s), désactivées si prefers-reduced-motion.

# Niveau 2 : Chartes des projets

Chaque charte de projet hérite du niveau 1. Elle ne contient que ce qui est propre au projet.

## Hôtellerie (Planning des réservations)

Hérite du niveau 1. Référence visuelle par défaut pour les futures applications.

- Barres de séjour continues : #E9E9E9, texte #1A1A1A 13 px graisse 500, rayon 6 px, texte tronqué par des points de suspension.
- Vue condensée : barres #BDBDBD de 6 px, rayon 2 px.
- Jour courant : en-tête encre graisse 600, soulignement 2 px encre, colonne teintée rgba(0,0,0,0.025).
- En-têtes de jours : 12 px, graisse 500, texte secondaire.
- Colonne des noms fixe de 140 px.
- Sélecteur de période : mois affiché 16,5 px graisse 600, boutons ‹ ›, « Aujourd'hui », contrôle segmenté « Semaine | Mois | Plage ».

## Arborescence (Préparation du projet)

Hérite du niveau 1.

- Cases grises sans bleu, même hauteur par ligne, largeur selon le texte, aucun trait de liaison.
- Seul le niveau affiché est visible ; fil d'Ariane collant en haut, 12 px, texte secondaire.
- Lignes de cases espacées de 18 px, cases espacées de 10 px.

## Autres projets

Emplacement à compléter. Chaque nouvelle charte hérite du niveau 1.
