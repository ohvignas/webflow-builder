# Inventaire Composants — {SITE_NAME}

**Site ID :** {SITE_ID}
**Date :** {DATE}

---

## Composants globaux (présents sur toutes les pages)

| Nom | Element ID | Component ID | Notes |
|-----|-----------|-------------|-------|
| Navbar | {ID} | {ID} | Header principal |
| Footer | {ID} | {ID} | Footer principal |

## Composants réutilisables

| Nom | Element ID | Component ID | Usage |
|-----|-----------|-------------|-------|
| Button Primary | {ID} | {ID} | CTA principal |
| Button Secondary | {ID} | {ID} | CTA secondaire |
| Feature Card | {ID} | {ID} | Carte de feature |
| Pricing Card | {ID} | {ID} | Carte de prix |
| FAQ Item | {ID} | {ID} | Question/réponse |

---

## Règle d'utilisation des composants

TOUJOURS utiliser les ComponentInstance existants pour navbar et footer.
Ne JAMAIS recréer ces éléments de zéro.

Pour copier un composant sur une nouvelle page :
- Utiliser de_page_tool → switch_page vers la page source
- Récupérer l'ID du composant
- Switcher sur la page cible
- Utiliser element_builder avec le bon component ID

---

## Styles de blocs courants

| Style | Combo class | Description |
|-------|-------------|-------------|
| hero_component | — | Wrapper interne du hero |
| content-top-section | — | Colonne de contenu gauche |
| hero_content-left_icon-tag | — | Badge/tag au-dessus du titre |
| DS Section Header | — | En-tête de section (titre + description) |
| flex-hori | gap-small | Flex horizontal espacé |
| flex-hori | gap-large | Flex horizontal très espacé |
| Column Large | — | Colonne large |
| Feature Card | — | Carte de feature avec padding |
| Pricing Card | — | Carte de prix |
| FAQ Border | — | Item FAQ avec bordure |
| chiffre-grid | — | Grille de stats/chiffres |
| Button Primary | — | Bouton CTA principal |
| round-btn | — | Bouton secondaire arrondi |
