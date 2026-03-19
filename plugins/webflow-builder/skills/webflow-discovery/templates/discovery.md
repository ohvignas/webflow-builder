# Discovery — {SITE_NAME}

**Site ID :** {SITE_ID}
**Date de discovery :** {DATE}
**URL :** {SITE_URL}
**Dernière publication :** {LAST_PUBLISHED}

---

## Vue d'ensemble

{Description du site en 2-3 phrases : type de site, objectif, stack}

---

## Design System

### Typographie

| Niveau | Classes | Usage |
|--------|---------|-------|
| H1 | (par défaut) | Titre principal de page |
| H2 | (par défaut) | Titre de section |
| H3 | (par défaut) | Titre de carte/bloc |
| Paragraphe Large | Paragraphe, Large | Sous-titres hero |
| Paragraphe Normal | Paragraphe | Corps de texte |
| Paragraphe Petit | Paragraphe, Small | Légendes, notes |

### Couleurs

| Nom | Classe CSS | Hex/Usage |
|-----|-----------|-----------|
| Rose principal | bg-rose / color-rose | Accent principal |
| Blanc | color-white | Texte sur fond sombre |
| Gris fond | Background Gray | Sections alternées |
| Brun clair | Background Brown 50 | Sections CTA |

### Spacers

| Taille | Classe | Usage |
|--------|--------|-------|
| 16px | spacer 2 + 16 | Espacement minimal |
| 32px | spacer 2 + 32 | Entre éléments internes |
| 64px | spacer 2 + 64 | Entre sections légères |
| 128px | spacer 2 + 128 | Entre sections majeures |

### Border-radius

| Valeur | Classe |
|--------|--------|
| 30px | radius + 30 |
| 20px | radius + 20 |
| 15px | radius + 15 |
| 10px | radius + 10 |

---

## Sections disponibles

| Style Section | Combo class | Usage typique |
|--------------|-------------|---------------|
| Hero Heading Left 2 | heo | Hero principal |
| Section Large 6 | — | Contenu 2 colonnes |
| Section Large 7 | — | Grille de features |
| Section Large 8 | — | Programme / modules |
| Section Regular | Background Brown 50 | CTA coloré |
| Section Regular 2 | Background Gray | FAQ / contenu léger |

## Containers

| Style | Usage |
|-------|-------|
| container-large | Container principal centré |

---

## Patterns de page identifiés

{Décrire ici les patterns observés sur les pages existantes}

Exemple :
- Hero → Section Large (features) → Section Large (programme) → Section Regular CTA → FAQ → Hero CTA final

---

## Notes importantes

{Tout ce qui est spécifique à ce site et qu'il faut absolument savoir}

---

## Screenshots

| Page | Slug | Fichier |
|------|------|---------|
| Homepage | / | screenshots/home.png |
| {Nom page parente} | {slug} | screenshots/{slug-normalisé}.png |
| {Template CMS} | {slug} | screenshots/{slug-normalisé}.png |

*Screenshots pris avec le Webflow Designer MCP — reflètent l'état au moment de la discovery.*
*Normalisation slug : "/" → home.png, slashes remplacés par tirets (ex: /blog/post → blog-post.png)*

> Si l'outil screenshot n'était pas disponible : noter ici "Screenshots non disponibles (outil MCP absent)"
