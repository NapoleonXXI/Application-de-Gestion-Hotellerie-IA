# Audit tactile — planning des réservations

Lecture de `docs/tableau/index.html` seulement. Aucun code d’application n’a été modifié.

Les constats viennent du code, de l’historique des correctifs déjà tentés, et des pratiques actuelles : défilement sur le fil compositeur, écouteurs passifs, `requestAnimationFrame`, virtualisation, limites de Safari sur iPhone et iPad. Cet environnement n’a pas d’iPhone : l’impact ci-dessous est celui que ce code produit sur ces appareils, pas une mesure chronométrée.

Périmètre : vues Semaine et Mois (geste maison), et la vue Plage (défilement natif d’un grand tableau). Contraintes gardées pour la suite : inertie verticale, un seul cran horizontal avec aimant, zone morte de 10 px, cône de ±25°, pas de `touch-action: pan-y`, code court, charte respectée, aucun élément visuel ajouté.

## Conclusion

La géométrie du doigt est bonne et doit rester. Le gel et le lag ne viennent pas du calcul d’angle.

Chaque mouvement du doigt fait défiler la grille **sur le fil principal**, et la virtualisation **ajoute des éléments pendant ce mouvement**. Safari doit en plus composer une frise d’environ 856 jours, large de 12 000 px sur un iPhone en vue Mois à plus de 100 000 px sur un iPad en vue Semaine, avec un en-tête collant de cette même largeur. Tant que le fil principal n’a pas fini, le toucher suivant attend : l’écran se fige, puis rattrape en retard.

Le changement du 3 novembre (bande de lignes élargie, sélection de texte coupée sur la frise) enlève le menu iOS au maintien du doigt. Il laisse le défilement sur le fil principal. Une fois les 100 lignes montées, il rend chaque glissement horizontal plus lourd. `touch-action: pan-y` a déjà été mis en place puis retiré : on n’y revient pas.

Le plus petit correctif utile tient en trois gestes de code : écrire la position **une fois par image**, ne monter **que les lignes utiles** (et les retirer en sortant), empêcher le doigt horizontal de **parcourir toute la frise**. Deux déclarations CSS vont avec.

## Causes principales

1. **`touchmove` écrit `scrollTop` ou `scrollLeft` et annule le geste natif.** L’écouteur est non passif. Safari attend la fin de cette fonction avant de continuer le toucher. À 60 Hz, et à 120 Hz sur iPad Pro, dès que l’image précédente dépasse sa durée, les mouvements s’empilent. L’écran ne suit plus le doigt.

2. **La bande de lignes ne fait que grandir.** Elle part de la vue, plus 8 lignes, plus encore 17. Elle s’étend à chaque nouveau rang et ne se resserre jamais. Après un aller vertical, les 100 logements sont dans le DOM. Le cran horizontal suivant crée un paquet de 28 jours et y injecte toutes ces lignes d’un coup.

3. **Le bloc défilant et son en-tête collant font toute la plage 2026–2028.** L’origine `a0` est toujours janvier 2026. Le dépôt avait déjà réduit cette largeur « pour iOS », puis l’a rouverte pour éviter un re-rendu au changement de période. Safari gère mal un calque collant de cette taille : tuiles blanches, gel, page parfois tuée.

4. **`-webkit-overflow-scrolling: touch` est encore sur le bloc, avec `overflow: auto` et `touch-action: none`.** Depuis iOS 13 cette propriété n’apporte plus l’inertie, et ici elle contredit le geste maison. Le symptôme habituel est un doigt qui ne fait plus rien jusqu’au toucher suivant.

## Tableau

| Problème | Cause | Correctif le plus simple | Impact |
|---|---|---|---|
| Le doigt lag, puis l’écran ne répond plus | `touchmove` non passif, `preventDefault` dès le premier pixel, écriture de `scrollTop` / `scrollLeft` à chaque événement (l. 234-236) | Garder `touch-action: none`. Mémoriser le point et appliquer la position une fois par `requestAnimationFrame`. N’appeler `preventDefault` que si un essai sur iPhone montre que la page défile encore seule | Élevé. Gel et lag au doigt |
| À-coup au fling vertical et au swipe d’une période | `tlVirt` à chaque image de défilement (l. 139). Bande ±8 puis ±17, sans retour en arrière (l. 193-196). Un paquet neuf reçoit toutes les lignes déjà ouvertes | Fenêtre = lignes visibles + 4, qui rétrécit. Changer le DOM seulement quand le paquet change, ou quand l’indice de ligne avance d’au moins 4. Rien de tout cela dans `touchmove` | Élevé. Lag, gel au changement de semaine ou de mois |
| Gel, zones blanches, page tuée, surtout iPad en Semaine | Frise du 1er décembre 2025 au 5 avril 2028, 856 jours (l. 102-110). En-tête `position: sticky` de cette largeur. Bloc d’environ 320 px : ~21 500 px en Semaine. Bloc d’environ 1 000 px : ~108 000 px | Garder les données 2026–2028. Ne donner au bloc défilant que la période affichée et ses deux voisines. Les paquets existent déjà : poser leur `left` dans cette petite largeur | Élevé. Gels iOS les plus durs |
| Le planning ne bouge plus du tout | `.plw` porte `-webkit-overflow-scrolling: touch` (l. 24) et `.tlx .plw.tl` porte `touch-action: none` (l. 3) | Retirer `-webkit-overflow-scrolling` de ce bloc. Ajouter `overflow-anchor: none` pour que l’ajout de barres ne déplace pas `scrollTop` tout seul | Élevé. Écran inerte. Une ligne CSS |
| Un long swipe horizontal monte des mois hors écran, puis revient | Pendant le doigt, `scrollLeft` peut aller du premier au dernier cran (l. 228 et 236). Au relâchement, l’aimant ne bouge que d’une période (l. 238) | Borner le `scrollLeft` du geste au cran précédent et au suivant. La règle de relâchement reste | Moyen. Lag des longs glissements. Le geste colle à « une période » |
| Un swipe parti d’une barre ouvre la page Séjour et reconstruit tout | `pointerup` appelle `sjOpen` sans retester les 10 px (l. 303). `sjOpen` fait un `render()` de toute la frise | Si le doigt a bougé de 10 px ou plus, ne pas ouvrir le séjour. Le `scroll` appelle déjà l’annulation : ce test couvre un `pointermove` perdu | Moyen. Gel par reconstruction de la page |
| Une boucle d’animation tourne pendant tout le geste | Chaque `scroll` relance `chk` (l. 242), qui appelle `document.contains` à chaque image. `chk` remet son drapeau à zéro en entrant, donc un second `scroll` ouvre une deuxième boucle. Le plafond de 900 images a été ajouté parce que cette boucle s’échappait | Recaler à la fin de l’inertie et à la fin de l’aimant. La boucle de surveillance peut disparaître | Moyen. Travail en continu, saccades |
| La vue Plage saccade | Une cellule par jour vide, 100 logements sur 2 à 12 mois (l. 247) | Fusionner les jours vides d’affilée en un seul `colspan` | Moyen. Cet écran seulement |
| Crayon ou trackpad iPad sans effet sur la grille | Le geste n’écoute que les événements `touch`. `touch-action: none` bloque le défilement natif qui resterait | Après les correctifs ci-dessus : la même fonction sur `pointerdown` / `pointermove` / `pointerup`, avec `setPointerCapture` | Moyen au crayon. Nul au doigt |
| L’inertie ignore « réduire les animations » | `glide` et `an` ne lisent pas `prefers-reduced-motion`. La charte coupe les transitions dans ce cas | Poser la position finale, sans boucle | Faible. Pas un gel |

## Ce qui est déjà bien écrit

- Zone morte de 10 px, `atan2`, seuils 25° et 65° (le cône vertical fait bien 25°), diagonale sans déplacement, cran horizontal limité à −1, 0 ou +1, vitesse calculée sur les 100 dernières millisecondes.
- Au verrouillage horizontal, l’origine est reprise après la zone morte : la grille ne saute pas de 10 px.
- Les écouteurs sont passifs, sauf le `touchmove` qui doit pouvoir annuler le défilement. `preventDefault` n’est appelé que si l’événement est annulable.
- Paquets de 28 jours, fragment de document, en-têtes mis en cache, barres ajoutées en complément quand la fenêtre grandit, une seule `requestAnimationFrame` par salve de `scroll`.
- Un seul bloc défile dans les deux axes. Les noms restent alignés sur la grille sans second écouteur de synchronisation.
- Pas de `elementFromPoint` sur le chemin du doigt. Sélection et menu contextuel iOS coupés sur la frise. Boutons en `touch-action: manipulation` (pas de délai au toucher).
- L’inertie verticale part d’une position mémorisée et ne relit pas la mise en page à chaque image. Un nouveau toucher l’arrête.
- `TL.halt` coupe les boucles avant de retirer le nœud.

## Ordre proposé

1. CSS du planning : retirer `-webkit-overflow-scrolling: touch`, ajouter `overflow-anchor: none` sur `.plw.tl`.
2. Dans `touchmove` : garder le point, écrire `scrollTop` ou `scrollLeft` une fois par image.
3. Dans `tlVirt` : fenêtre glissante qui rétrécit, mutation seulement au changement de paquet ou par bond de lignes.
4. Borner le glissement horizontal au cran voisin.
5. Au `pointerup`, ne pas ouvrir le séjour si le déplacement atteint 10 px.
6. Remplacer la boucle `chk` par un recalage en fin d’inertie et en fin d’aimant.
7. Réduire ensuite la largeur réelle du bloc à trois périodes. C’est le correctif des gels iPad les plus durs. Les six premiers se jugent sans lui, et restent courts.

`will-change` et `translateZ(0)` sur toute la frise augmentent la mémoire des calques Safari et empirent ce type de gel. On les garde pour un calque précis, le temps d’un geste, seulement si les sept points ci-dessus ne suffisent pas.

Le défilement vertical natif (`touch-action: pan-y`, essai `8d979b0`, retiré par `5dba99a`) sortirait l’inertie verticale du contrat actuel. Il n’est pas dans la liste.

## Vérification, le jour où le code bougera

Sur iPhone et iPad, vues Semaine et Mois : fling vertical sur toute la hauteur, swipe d’un cran, diagonale immobile, doigt qui reste sous 10 px, swipe qui part d’une barre (le planning reste, Séjour ne s’ouvre pas), second doigt pendant l’inertie. Dans l’inspecteur distant : pas de tâche longue dans `touchmove`, peu de nœuds ajoutés par image. Vue Plage : les jours vides ne sont plus une cellule chacun.
