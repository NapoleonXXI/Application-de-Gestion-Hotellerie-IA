# Charte de design

Référence par défaut de toutes les applications : le Planning des réservations (docs/tableau/index.html), complété par la page Préparation du projet. On reprend cette base, on ne redéfinit pas un design à chaque application.

## 1. Principes

- Credo : « La simplicité est la sophistication ultime ».
- Gris minimaliste : blanc, gris et encre. Aucune couleur décorative.
- Seulement ce qui est utile à l'écran. Ce qui n'aide pas la tâche n'apparaît pas.
- Vues condensées qui tiennent à l'écran, sans défilement inutile.
- Fins séparateurs gris (1 px) placés derrière le contenu, jamais devant.
- Aucun indicateur superflu : pas de badge, d'icône ou d'animation sans fonction.

## 2. Couleurs

| Rôle | Clair | Sombre |
|---|---|---|
| Fond | #FFFFFF | #121212 |
| Texte principal (encre) | #1A1A1A | #EDEDED |
| Texte secondaire | #5C5C5C (planning), #8A8A8A (arborescence, icônes) | #ABABAB |
| Bordure | #E4E4E4 | #333333 |
| Remplissage léger (segments, appui) | #F0F0F0 | #262626 |
| Barre de séjour | #E9E9E9, texte #1A1A1A | |
| Barre condensée | #BDBDBD | |
| Mois sélectionné dans une plage | #DCDCDC | |
| Séparateurs de lignes | #EFEFEF | |
| Séparateurs de colonnes | #ECECEC | |
| Colonne du jour | rgba(0,0,0,0.025) | |
| Ombres | rgba(0,0,0,0.08) à 0.14 | |

Le bleu n'est pas interdit en soi. Il est réservé à un usage demandé et justifié (par exemple un signal explicitement souhaité), jamais ajouté par défaut.

## 3. Typographie

- Famille : Inter, puis -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif.
- Graisses : 400 (texte courant), 500 (libellés, titres), 600 (jour courant, mois affiché, accent).
- Tailles : 16 à 16,5 px (titres, mois), 15 px (boutons, lignes de détail), 14 px (cases, segments), 13 px (noms de lignes, barres, sous-titres), 12 px (en-têtes de jours, fil d'Ariane, notes), 11 px (barres compactes, indications), 9 px (mentions minimes).
- Chiffres tabulaires : font-variant-numeric: tabular-nums dans les tableaux.

## 4. Espacements

- Marge de page : 16 px.
- Écart entre éléments : 8 à 10 px ; entre lignes de cases : 18 px.
- Cases et boutons : padding 10 px vertical, 14 px horizontal.
- Cellules : padding 8 à 10 px ; hauteur de ligne 44 px.
- Cible tactile minimale : 44 px.
- Rayons : 6 px (barres), 8 px (cases, boutons), 10 px (cartes), 12 px (panneaux flottants).

## 5. Composants

- Cases : gris, bordure 1 px #E4E4E4, fond transparent, rayon 8 px. Même hauteur sur une ligne, largeur ajustée au texte (min 120 px, max 260 px). Aucun trait de liaison entre cases.
- Boutons : bordure 1 px #E4E4E4, rayon 8 px, fond transparent, texte encre 15 px, hauteur 44 px.
- Contrôle segmenté : fond #F0F0F0, rayon 9 px ; segment actif fond blanc, texte encre, ombre 0 1px 2px rgba(0,0,0,0.12).
- Barres de séjour : continues, #E9E9E9, rayon 6 px, texte 13 px graisse 500, coupé par des points de suspension.
- Jour courant : texte encre graisse 600, soulignement 2 px encre, colonne légèrement teintée.
- Panneau flottant : fond blanc, bordure #E4E4E4, rayon 12 px, ombre 0 6px 20px rgba(0,0,0,0.08).

## 6. États

- Survol : changement discret de fond (#F0F0F0), jamais de couleur vive.
- Appui (:active) : fond #F0F0F0.
- Sélectionné : fond encre #1A1A1A, texte blanc ; intervalle #DCDCDC.
- Désactivé : texte secondaire, sans action, sans couleur supplémentaire.
- Focus clavier : discret et visible uniquement au clavier via :focus-visible (contour 1 px #D6D6D6, décalage 1 px). Jamais de cadre noir. Jamais de focus programmatique sur les titres.
- Mouvement : transitions courtes (0,18 s) et désactivées si prefers-reduced-motion.

## 7. Règle « aucun élément visuel non demandé »

Aucun élément visuel ne s'ajoute sans demande explicite. Exemples à ne pas reproduire :
- la case bleue apparue sur l'arborescence ;
- le cadre noir de focus apparu sur le titre.

Toute nouveauté visuelle est proposée à part et validée avant d'entrer dans une version validée.
