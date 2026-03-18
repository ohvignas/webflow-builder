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
- [ ] 3. Trouver la page Style Guide (chercher "style" dans les titres de pages)
- [ ] 4. Switcher sur la page Style Guide (de_page_tool → switch_page)
- [ ] 5. Lire tous les éléments du style guide (element_tool → get_all_elements)
- [ ] 6. Extraire et documenter :
         - Palette de couleurs (noms des classes bg-*, color-*)
         - Échelle typographique (H1→H6, paragraphe large/normal/petit)
         - Système de spacers (classes spacer + tailles disponibles)
         - Border-radius disponibles
         - Styles de boutons (classes + combo classes)
         - Composants (ComponentInstance trouvés)
- [ ] 7. Switcher sur une page de contenu similaire au besoin de l'utilisateur
- [ ] 8. Lire sa structure (element_tool → get_all_elements)
- [ ] 9. Documenter les patterns de sections (Section + styles, Container + styles, blocs internes)
- [ ] 10. Vérifier si les docs existent déjà dans ~/.claude/webflow/{site-id}/
- [ ] 11. Sauvegarder les 4 fichiers (discovery.md, components.md, styles.md, pages.md)
- [ ] 12. Annoncer à l'utilisateur que la discovery est complète + résumé

## Règles techniques

- Les éléments retournés par element_tool sont plats (pas de tree). Pour reconstituer
  la hiérarchie, observer l'ordre des éléments : les enfants suivent leurs parents.
- Les styles s'appliquent via `set_style: { style_names: ["NomDuStyle", "ComboClass"] }`
- L'element_builder a une limite de 3 niveaux de profondeur. Planifier la structure
  en conséquence.
- Les ComponentInstance (navbar, footer) ne se recréent pas — on les copie ou on
  les référence par ID.

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
