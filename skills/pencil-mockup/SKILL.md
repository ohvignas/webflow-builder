---
name: pencil-mockup
description: Use when the user wants to create a visual mockup before building in Webflow. Creates .pen wireframes and styled mockups using the Pencil MCP tools. Requires design documents (PRD, style guide, sitemap, copy deck) to exist first. Also use when the user says "mockup", "maquette", "wireframe", or "design dans Pencil".
---

# Pencil Mockup

Créer des maquettes visuelles dans Pencil (.pen) à partir des documents de design,
pour valider le rendu avant de construire sur Webflow.

<HARD-GATE>
Ne jamais commencer une maquette sans avoir lu les documents de design :
- style-guide.md (couleurs, fonts, espacements)
- sitemap.md (structure des pages et sections)
- copy-deck.md (textes réels)
Si ces documents n'existent pas, invoquer webflow-new-project EN PREMIER.
</HARD-GATE>

## Prérequis

Le MCP Pencil doit être disponible (extension Pencil installée dans l'IDE).
Vérifier avec `get_editor_state`. Si non disponible, informer l'utilisateur :
"Le MCP Pencil n'est pas détecté. Installe l'extension Pencil dans ton IDE
(VS Code / Cursor) pour créer des maquettes. On peut continuer sans maquette
et aller directement sur Webflow."

## Checklist

### Phase 1 : Préparation

- [ ] 1. Lire les 4 documents de design :
         - `~/.claude/webflow/{project}/prd.md`
         - `~/.claude/webflow/{project}/style-guide.md`
         - `~/.claude/webflow/{project}/sitemap.md`
         - `~/.claude/webflow/{project}/copy-deck.md`
- [ ] 2. Identifier les pages à maquetter (depuis le sitemap)
- [ ] 3. Demander à l'utilisateur quelles pages maquetter en priorité
         (proposer : "Toutes" ou lister par priorité P0 → P1 → P2)
- [ ] 4. Appeler `get_editor_state(include_schema: true)` pour charger le schéma Pencil
- [ ] 5. Appeler `get_guidelines(topic: "landing-page")` ou `get_guidelines(topic: "web-app")`
         selon le type de site
- [ ] 6. Appeler `get_style_guide_tags()` puis `get_style_guide(tags: [...])` avec des tags
         correspondant au style du projet

### Phase 2 : Wireframe (structure)

Pour chaque page à maquetter :

- [ ] 7. Créer ou ouvrir un fichier .pen :
         `open_document("~/.claude/webflow/{project}/mockups/{page-name}.pen")`
- [ ] 8. Créer le design system local dans le .pen :
         - Définir les variables de couleur (via `set_variables`)
         - Créer les composants réutilisables (boutons, cartes, inputs)
- [ ] 9. Créer le wireframe desktop (1440px wide) :
         - Créer un frame "Desktop - {Page Name}" (1440 x auto)
         - Builder section par section selon le sitemap
         - Utiliser les vrais textes du copy-deck
         - Structure uniquement : rectangles gris pour les images, pas de style
- [ ] 10. Prendre un screenshot (`get_screenshot`) pour valider
- [ ] 11. Montrer le wireframe à l'utilisateur pour validation de la structure

### Phase 3 : Maquette stylée

- [ ] 12. Appliquer le style guide sur le wireframe validé :
          - Couleurs (fills, backgrounds)
          - Typographie (font family, sizes, weights)
          - Espacements (padding, gap)
          - Border-radius
          - Ombres
- [ ] 13. Remplacer les placeholders images par des images stock/AI :
          - Utiliser `G(nodeId, "stock", "description")` pour les photos
          - Utiliser `G(nodeId, "ai", "prompt")` pour les illustrations
- [ ] 14. Créer la version mobile (390px wide) :
          - Copier le screen desktop
          - Adapter le layout (colonnes → empilé, tailles réduites)
- [ ] 15. Prendre des screenshots des deux versions
- [ ] 16. Soumettre la maquette complète à l'utilisateur pour validation

### Phase 4 : Export et documentation

- [ ] 17. Exporter les screens en PNG :
          `export_nodes(filePath, outputDir: "~/.claude/webflow/{project}/mockups/", nodeIds, format: "png")`
- [ ] 18. Créer un PDF récapitulatif si plusieurs pages :
          `export_nodes(filePath, outputDir, nodeIds, format: "pdf")`
- [ ] 19. Annoncer que les maquettes sont prêtes et proposer :
          "Les maquettes sont prêtes. On passe à la construction sur Webflow ?"

## Règles de design dans Pencil

### Structure d'un screen

```
Frame "Desktop - {Page}" (width: 1440, layout: vertical)
├── Frame "Navbar" (width: fill, height: 80, layout: horizontal, padding: 0 80)
│   ├── Text "Logo" (fontSize: 24, fontWeight: bold)
│   └── Frame "Nav Links" (layout: horizontal, gap: 32)
├── Frame "Section Hero" (width: fill, layout: vertical, padding: 80)
│   └── Frame "Container" (width: 1200, layout: vertical, gap: 24)
│       ├── Text "H1" (fontSize: 56)
│       ├── Text "Subtitle" (fontSize: 20)
│       └── Frame "CTA" (layout: horizontal, gap: 16)
├── Frame "Section Features" (width: fill, padding: 80)
│   └── Frame "Container" (width: 1200, layout: vertical, gap: 48)
│       ├── Text "H2" (fontSize: 40)
│       └── Frame "Grid" (layout: horizontal, gap: 24)
│           ├── Frame "Card" (width: fill, layout: vertical)
│           ├── Frame "Card" (width: fill, layout: vertical)
│           └── Frame "Card" (width: fill, layout: vertical)
└── Frame "Footer" (width: fill, padding: 48 80)
```

### Tailles standard

| Élément | Desktop | Mobile |
|---------|---------|--------|
| Screen width | 1440px | 390px |
| Container max-width | 1200px | 350px |
| Section padding vertical | 80px | 48px |
| Section padding horizontal | 80px | 20px |
| Navbar height | 80px | 64px |
| H1 | 48-64px | 32-40px |
| H2 | 36-48px | 28-36px |
| Body text | 16-18px | 16px |

### Images

- JAMAIS de node `type: "image"` — ça n'existe pas dans Pencil
- Créer un `frame` ou `rectangle`, puis appliquer une image avec `G()`
- `G(nodeId, "stock", "keywords")` pour les photos réalistes
- `G(nodeId, "ai", "detailed prompt")` pour les illustrations personnalisées

### Composants réutilisables

Créer des composants (`reusable: true`) pour :
- Bouton Primary
- Bouton Secondary
- Carte de feature
- Input de formulaire
- Navbar
- Footer

Puis les instancier avec `type: "ref"` et personnaliser via `U()`.

### Batch design

- Max 25 opérations par appel `batch_design`
- Splitter par section logique : d'abord la structure du screen, puis la navbar,
  puis le hero, puis chaque section
- Toujours créer un binding pour chaque Insert/Copy/Replace
- Le binding `document` est prédéfini — l'utiliser pour les screens top-level

## Erreurs courantes à éviter

| Erreur | Correction |
|--------|-----------|
| Créer un node type "image" | Utiliser un frame + G() pour appliquer l'image |
| Tout mettre dans un seul batch_design | Splitter en 25 ops max par appel |
| Oublier le binding sur Insert | Chaque I(), C(), R() DOIT avoir un binding |
| Réutiliser des binding names | Toujours des noms uniques par batch_design |
| Ne pas utiliser les vrais textes | Charger le copy-deck et utiliser les vrais contenus |
| Deviner les couleurs | Lire le style-guide.md et utiliser les hex définis |
| Sauter le wireframe | Toujours valider la structure avant de styler |
