---
name: webflow-build-page
description: Use when asked to create a new page, section, or layout on a Webflow site. Recommends creating a Pencil mockup first if none exists. Requires discovery documents to exist. Never start building without reading the discovery docs. Also use when the user says "build page", "create page", "add a page", or "nouvelle page".
---

# Webflow Build Page

Construire une page Webflow qui respecte exactement la DA du site existant.
Recommande de créer une maquette Pencil avant de builder, mais c'est optionnel.

<HARD-GATE>
Ne jamais créer un seul élément avant d'avoir :
1. Chargé les docs de discovery depuis ~/.claude/webflow/{site-id}/
2. Identifié la page existante la plus similaire
3. Écrit et fait valider un plan de page
</HARD-GATE>

## Flow complet

```
Vérifier discovery docs
  → Proposer maquette Pencil (recommandé mais optionnel)
    → Si oui : créer les docs de design (style-guide, sitemap section, copy-deck section)
             → invoquer pencil-mockup
             → valider la maquette
    → Si non : continuer
  → Écrire le plan de page
  → Faire valider le plan
  → Builder sur Webflow
```

## Checklist

### Phase 0 : Vérification des prérequis

- [ ] 1. Vérifier l'existence de `~/.claude/webflow/{site-id}/discovery/discovery.md`
         → Si absent : invoquer `webflow-discovery` EN PREMIER
- [ ] 2. Lire les docs de discovery : discovery.md, components.md, styles.md, pages.md
- [ ] 3. Vérifier si des docs de design existent déjà :
         `~/.claude/webflow/{site-id}/style-guide.md`, `sitemap.md`, `copy-deck.md`

### Phase 1 : Proposition maquette Pencil (recommandée)

- [ ] 4. Demander à l'utilisateur :
         "Avant de construire, je recommande de créer une maquette rapide
         dans Pencil pour valider le visuel. Ça prend quelques minutes et
         ça évite les allers-retours sur Webflow. On fait la maquette ?"
- [ ] 5. Si oui :
         a. Si les docs de design n'existent pas, les créer :
            - Rédiger un mini style-guide basé sur les docs de discovery
            - Rédiger la section sitemap pour cette page
            - Rédiger le copy-deck pour cette page
            - Faire valider chaque document
         b. Invoquer `pencil-mockup` avec les docs
         c. Une fois la maquette validée, continuer
      Si non :
         a. Passer directement au plan

### Phase 2 : Plan de page

- [ ] 6. Identifier la page existante la plus similaire au besoin
- [ ] 7. Switcher sur cette page + lire sa structure (element_tool → get_all_elements)
- [ ] 8. Écrire le plan de la nouvelle page :
         - Liste ordonnée des sections
         - Pour chaque section : style Section + style Container + styles internes
         - Textes / contenu prévu (depuis le copy-deck ou la maquette)
         - Si une maquette Pencil existe : la référencer comme source de vérité
- [ ] 9. Soumettre le plan à l'utilisateur pour validation AVANT de toucher quoi que ce soit

### Phase 3 : Construction Webflow

- [ ] 10. Créer la page (de_page_tool → create_page)
- [ ] 11. Mettre à jour le slug et le SEO (data_pages_tool → update_page_settings)
- [ ] 12. Switcher sur la nouvelle page
- [ ] 13. Récupérer le Body ID
- [ ] 14. Builder section par section :
          a. Navbar (ComponentInstance existant — NE JAMAIS recréer)
          b. Hero
          c. Sections de contenu (de haut en bas)
          d. CTA final
          e. Footer (ComponentInstance existant)
- [ ] 15. Après chaque section majeure : prendre un screenshot pour valider visuellement
          Si une maquette Pencil existe, comparer avec la maquette
- [ ] 16. Ajouter les spacers entre chaque section
- [ ] 17. Vérifier navbar + footer (ComponentInstance)

### Phase 4 : Vérification

- [ ] 18. Screenshot complet de la page
- [ ] 19. Comparer avec la maquette Pencil si elle existe
- [ ] 20. Vérifier le SEO : slug, title, description, OpenGraph
- [ ] 21. Annoncer la page terminée et proposer :
          - Utiliser `accessibility-audit` (skill officiel)
          - Utiliser `link-checker` (skill officiel)
          - Utiliser `safe-publish` (skill officiel)

## Règles de construction

### Hiérarchie des sections (pattern standard)
```
Section [style-section + combo]
  └── Container [container-large]
        └── DivBlock [wrapper interne]   ← niveau 3 max pour element_builder
```
Pour aller plus profond : créer le wrapper d'abord, récupérer son ID,
puis faire un nouvel appel element_builder.

### Spacers obligatoires entre sections
- `spacer 2` + `64` entre sections légères
- `spacer 2` + `128` entre sections majeures

### SEO obligatoire sur chaque page
- slug (URL propre, kebab-case)
- seo.title (format : "Titre Page | Nom Site")
- seo.description (150-160 caractères, mots-clés naturels)
- openGraph.title
- openGraph.description

## Erreurs courantes à éviter

| Erreur | Correction |
|--------|-----------|
| Recréer la navbar de zéro | Utiliser le ComponentInstance existant |
| Ignorer les spacers | Ajouter spacer 64 ou 128 entre chaque section |
| Deviner les noms de classes | Lire discovery.md + styles.md d'abord |
| Builder sans plan validé | Toujours faire valider le plan avant |
| Dépasser 3 niveaux en une fois | Splitter en plusieurs appels element_builder |
| Ne pas prendre de screenshots | Valider visuellement après chaque section majeure |
| Ignorer la maquette Pencil | Si elle existe, c'est la source de vérité visuelle |
| Ne pas proposer les audits | Toujours proposer accessibility + link-checker après |
