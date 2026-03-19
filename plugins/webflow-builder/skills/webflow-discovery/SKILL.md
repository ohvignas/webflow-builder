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
             (vérifier le nom exact du paramètre si l'appel échoue : peut s'appeler ids ou style_ids)
- [ ] 5. Trouver la page Style Guide (chercher "style" dans les titres de pages)
         Si aucune page ne contient "style" dans son titre : passer directement à l'étape 8,
         documenter l'absence de Style Guide dans discovery.md
- [ ] 6. Switcher sur la page Style Guide (de_page_tool → switch_page)
- [ ] 7. Lire la structure du Style Guide (element_tool → get_all_elements(include_style_properties: false))
         → retourne le tableau `styles` de chaque élément (noms des classes appliquées)
         → identifier les combos de classes et les ComponentInstance (navbar, footer) avec leurs IDs
         Si Claude Code retourne "exceeds maximum allowed tokens" :
           → Ignorer cette étape — les classes CSS sont déjà disponibles depuis get_styles (étape 4)
           → Documenter dans discovery.md que les combos de classes et ComponentInstance IDs
             n'ont pas pu être extraits (nécessitera une consultation manuelle)
- [ ] 8. Extraire et documenter :
         - Palette de couleurs (depuis variable_tool)
         - Échelle typographique (depuis variable_tool)
         - Classes disponibles avec propriétés CSS (depuis get_styles)
         - Combos de classes (depuis structure Style Guide)
         - Composants : ComponentInstance avec IDs (navbar, footer)
- [ ] 9. Vérifier si les docs existent déjà dans ~/.claude/webflow/{site-id}/
- [ ] 10. Sauvegarder les 4 fichiers (discovery.md, components.md, styles.md, pages.md)
- [ ] 11. Annoncer à l'utilisateur que la discovery est complète + résumé
- [ ] 12. Phase visuelle — screenshots des pages prioritaires (conditionnel — outil MCP requis) :

         Identifier les pages prioritaires depuis la liste de l'étape 2 :
           a. Homepage : page dont le slug est "/" ou première page sans parentId
           b. Pages parentes : pages dont l'id apparaît comme parentId d'autres pages
           c. Templates CMS : pour chaque groupe avec même parentId, prendre la page
              avec lastUpdated le plus récent (lastUpdated est présent dans list_pages,
              format ISO 8601 ex: "2026-03-16T23:33:41.071Z")
              Si deux pages ont le même lastUpdated, prendre la première
              Si lastUpdated absent, prendre la première du groupe
           d. Maximum 15 pages au total

         Avant de commencer : vérifier parmi les outils disponibles en contexte si l'un
         contient "screenshot", "capture" ou "image".
         Si aucun outil screenshot n'existe : noter "Screenshots non disponibles (outil MCP absent)"
         dans discovery.md et terminer — NE PAS bloquer la discovery.

         Pour chaque page prioritaire (si outil screenshot disponible) :
           a. de_page_tool → switch_page
           b. Prendre un screenshot avec l'outil trouvé
           c. Normaliser le slug en nom de fichier :
              "/" → "home.png"
              autres → remplacer tous "/" par "-", supprimer le "-" initial
              ex: "/blog/article-1" → "blog-article-1.png"
           d. Sauvegarder dans ~/.claude/webflow/{site-id}/screenshots/{nom-normalisé}.png
              (créer le dossier screenshots/ si absent)
           e. Si switch_page ou screenshot échoue : passer à la page suivante sans bloquer

         Mettre à jour la section ## Screenshots de discovery.md avec la liste des captures.

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
- Phase visuelle (étape 12) : conditionnelle à la présence d'un outil screenshot dans
  le MCP Webflow connecté. Si absent, ignorer silencieusement.
  Logique de priorisation des pages :
  1. Homepage = slug "/" ou première page sans parentId
  2. Pages parentes = pages dont l'id apparaît comme parentId d'autres pages
  3. Templates CMS = une page par groupe de même parentId (la plus récente par lastUpdated)
  4. Maximum 15 pages
  Normalisation slug → nom de fichier : "/" → "home.png", "/" remplacés par "-", "-" initial supprimé
  ex: "/blog/article-1" → "blog-article-1.png"

## Output attendu

Après cette skill, 4 fichiers doivent exister dans ~/.claude/webflow/{site-id}/ :
- discovery.md (vue d'ensemble)
- components.md (tous les composants avec IDs)
- styles.md (toutes les classes avec usage)
- pages.md (toutes les pages avec slug, parentId, publishedPath)
- screenshots/ (optionnel — si outil screenshot MCP disponible)

## Quand relancer

Relancer si :
- Le site a été mis à jour (nouveau composant, nouvelle couleur)
- Les fichiers de discovery ont plus de 30 jours
- L'utilisateur signale un changement dans la DA
