---
name: webflow-discovery
description: Use when starting any session on an existing Webflow site, before creating or editing any page, element, or style. Use when the user asks to build, edit, or audit a Webflow site and no discovery documents exist yet.
---

# Webflow Discovery

Scanner complètement un site Webflow et sauvegarder sa DA, ses composants et
ses patterns dans des documents persistants pour les prochaines sessions.

<HARD-GATE>
Ne jamais créer ou modifier un élément Webflow avant d'avoir complété
la discovery et sauvegardé les documents. Sans cette étape, tu vas
reconstruire ce qui existe déjà et casser la cohérence visuelle du site.
</HARD-GATE>

## Checklist (dans l'ordre)

- [ ] 1. Récupérer l'ID et le nom du site (mcp: data_sites_tool → list_sites)
- [ ] 2. Lister toutes les pages (data_pages_tool → list_pages)
- [ ] 3. Récupérer les variables design :
         variable_tool → get_variable_collections
         variable_tool → get_variables pour chaque collection
         → couleurs, typographie, espacements
- [ ] 4. Récupérer toutes les classes CSS :
         style_tool → get_styles(query: "all", skip_properties: false)
         Si Claude Code retourne "exceeds maximum allowed tokens" :
           → get_styles(query: "all", skip_properties: true)
           → identifier les classes importantes : btn-*, heading-*, section-*, container-*, spacer-*
             Si aucune classe ne correspond, prendre les 20 premières classes de la liste
           → get_styles(query: "filtered", filter_ids: [IDs des classes identifiées])
- [ ] 5. Trouver la page Style Guide (chercher "style" dans les titres de pages)
- [ ] 6. Switcher sur la page Style Guide (de_page_tool → switch_page)
- [ ] 7. Lire la structure du Style Guide (element_tool → get_all_elements(include_style_properties: false))
         → retourne le tableau `styles` de chaque élément (noms des classes appliquées)
         → identifier les combos de classes et les ComponentInstance (navbar, footer) avec leurs IDs
         Si Claude Code retourne "exceeds maximum allowed tokens" :
           → Ignorer cette étape — les classes et ComponentInstance IDs sont déjà disponibles
             depuis get_styles (étape 4) et list_pages (étape 2)
           → Documenter dans discovery.md que les combos de classes n'ont pas pu être extraits
- [ ] 8. Extraire et documenter :
         - Palette de couleurs (depuis variable_tool)
         - Échelle typographique (depuis variable_tool)
         - Classes disponibles avec propriétés CSS (depuis get_styles)
         - Combos de classes (depuis structure Style Guide)
         - Composants : ComponentInstance avec IDs (navbar, footer)
- [ ] 9. Vérifier si les docs existent déjà dans ~/.claude/webflow/{site-id}/
- [ ] 10. Sauvegarder les 4 fichiers (discovery.md, components.md, styles.md, pages.md)
- [ ] 11. Annoncer à l'utilisateur que la discovery est complète + résumé

## Règles techniques

- Les éléments retournés par element_tool sont plats (pas de tree). Pour reconstituer
  la hiérarchie, observer l'ordre des éléments : les enfants suivent leurs parents.
- Les styles s'appliquent via `set_style: { style_names: ["NomDuStyle", "ComboClass"] }`
- L'element_builder a une limite de 3 niveaux de profondeur. Planifier la structure
  en conséquence.
- Les ComponentInstance (navbar, footer) ne se recréent pas — on les copie ou on
  les référence par ID.
- Ne jamais appeler `get_all_elements` sans préciser `include_style_properties: false`.
  La valeur par défaut retourne toutes les propriétés CSS de chaque élément et dépasse
  la limite de tokens pour toute page complexe. Signal d'overflow : Claude Code retourne
  "exceeds maximum allowed tokens" dans le résultat de l'outil.
  Note : avec `false`, le tableau `styles` de chaque élément (noms des classes appliquées)
  reste disponible — seules les valeurs CSS brutes sont exclues.

## Output attendu

Après cette skill, 4 fichiers doivent exister dans ~/.claude/webflow/{site-id}/ :
- discovery.md (vue d'ensemble)
- components.md (tous les composants avec IDs)
- styles.md (toutes les classes avec usage)
- pages.md (toutes les pages avec slug, parentId, publishedPath)

## Quand relancer

Relancer si :
- Le site a été mis à jour (nouveau composant, nouvelle couleur)
- Les fichiers de discovery ont plus de 30 jours
- L'utilisateur signale un changement dans la DA
