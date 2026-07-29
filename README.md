# Outil de priorisation PTE — v27.07.2026

## Les trois pages

| Fichier | Contenu | Poids | Dépendance externe |
|---|---|---|---|
| `pte-analyse.html` | Dashboard, Classement, Manuel | 111 Ko | Plotly (graphiques) |
| `pte-phasage.html` | Gantt 2025–2037, listes P1–P4 | 65 Ko | **aucune** |
| `pte-carte.html` | Carte interactive + filtres | 74 Ko | Leaflet (fond de carte) |

Les onglets de l'en-tête naviguent entre les pages ; l'apparence est identique à la version d'origine.

## Correction de l'import Excel

L'import passait par SheetJS, chargée depuis `cdn.jsdelivr.net`. Ce domaine n'étant pas
joignable depuis votre poste, la librairie n'existait pas au moment du clic et l'import
échouait silencieusement (le message d'erreur disparaissait au bout de trois secondes).

La page contient désormais **son propre lecteur `.xlsx`** — décompression ZIP, décodage
DEFLATE et analyse du XML compris, soit environ 8 Ko. L'import ne dépend plus d'aucune
librairie ni d'aucun accès réseau : il fonctionne hors ligne et derrière un pare-feu.

Le lecteur a été validé cellule par cellule contre SheetJS sur votre fichier :
**583 lignes × 25 colonnes, aucune divergence** (à l'exception des retours à la ligne dans
deux en-têtes, sans effet puisque les espaces sont ignorés lors de la reconnaissance des colonnes).

Le format `.csv` reste accepté. L'ancien format `.xls` (Excel 97-2003) ne l'est pas et
l'indique explicitement.

## Ce qui reste tributaire d'Internet

Les graphiques du dashboard et le fond de carte. Si ces ressources sont bloquées à leur tour,
un **bandeau orange permanent** l'indique en haut de page, et le reste de l'outil — import,
classement, filtres, phasage, notes — continue de fonctionner normalement.

## Export

L'export Excel a été retiré : l'outil ne modifie pas les données, seules les notes ont
vocation à en sortir. Restent donc deux exports ciblés :

- **📝 Notes** — CSV `N° ; Nom du site ; Note ; Date`, avec réimport. Fichier indépendant :
  les notes sont conservées à chaque mise à jour du fichier de données.
- **📥 Export Alertes** — CSV des anomalies détectées, depuis le dashboard.

Les deux protègent correctement les champs contenant `;`, guillemets ou sauts de ligne.

## Partage entre les trois pages

Importer le fichier **une seule fois** : sites, pondérations, notes, phases ajustées au Gantt
et coordonnées géocodées suivent automatiquement.

Le support dépend du navigateur : `localStorage` sous Chrome, Edge et Firefox ; sous Safari
en fichier local, où `localStorage` est refusé, l'outil bascule sur `window.name`, qui
transporte les données tant que vous restez **dans le même onglet** — ce que font les liens
d'en-tête. Le bouton **🗑** réinitialise le tout.

## Audit CECB+ — six statuts, trois familles

| Badge | Valeur dans le fichier | Points de faisabilité |
|---|---|---|
| ✗ rouge | `---` ou vide | 0 |
| ⏳ orange | `en cours` | +16 |
| 👤 orange | `à vérifier` | 0 |
| ✓ A / ✓ B / ✓ C vert | `var_A` / `var_B` / `var_C` | +33 |

Sur le fichier actuel : 138 verts, 16 oranges, 145 rouges.

## Deux points qui modifient les scores

- **Chauffage électrique direct** : il rapportait 0 au critère « CO₂ / Fossile », ce qui
  sous-priorisait 11 sites pourtant candidats à une substitution. Il rapporte désormais 45.
- **Audit CECB+ « en cours »** : il créditait auparavant la totalité des 33 points, à égalité
  avec un audit livré. Il n'en crédite plus que 16, et « à vérifier » plus rien.

## Autres corrections

- Suppression des deux balises `Content-Security-Policy` héritées de POE, qui bloquaient
  toutes les tuiles de la carte hors de cet environnement.
- Suppression de Tailwind CSS, dont aucune classe n'était utilisée, et du correctif
  Tailwind × Leaflet devenu inutile.
- Colonne « Pat. » sans fond coloré ; badges de chauffage complétés (⚡ Élec., 🔥♻️ PAC+Gaz,
  ⛔ Inexistant) ; colonne « État du projet (SIA) » importée et filtrable.

## Publication sur GitHub Pages

Déposer les trois fichiers à la racine du dépôt. Pour que l'outil s'ouvre directement sur
l'analyse, copier `pte-analyse.html` en `index.html` (les liens relatifs restent valides).
