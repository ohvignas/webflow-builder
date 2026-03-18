---
name: stitch-mockup
description: Use when the user wants to create UI mockups using Google Stitch before building in Webflow. Generates screens from text prompts via Stitch MCP, extracts design context, and downloads HTML/CSS code. Also use when the user says "stitch", "google stitch", "generate UI", or "mockup avec stitch".
---

# Stitch Mockup

Générer des maquettes UI via Google Stitch (Gemini 2.5 Pro) à partir des documents
de design, pour valider le visuel avant de construire sur Webflow.

<HARD-GATE>
Ne jamais commencer une maquette sans avoir lu les documents de design :
- style-guide.md (couleurs, fonts, espacements)
- sitemap.md (structure des pages et sections)
- copy-deck.md (textes réels)
Si ces documents n'existent pas, invoquer webflow-new-project EN PREMIER.
</HARD-GATE>

## Prérequis

### Installation du MCP Stitch

**Option A : Via le wrapper stitch-mcp (recommandé — auto-refresh token)**

```bash
# Setup guidé (gcloud + auth + config MCP)
npx @_davideast/stitch-mcp init
```

Ou ajouter manuellement dans la config MCP de Claude Code :
```json
{
  "mcpServers": {
    "stitch": {
      "command": "npx",
      "args": ["@_davideast/stitch-mcp", "proxy"]
    }
  }
}
```

**Option B : Via le package stitch-mcp universel**

```bash
# Prérequis : gcloud CLI installé et configuré
gcloud auth application-default login
gcloud config set project YOUR_PROJECT_ID
gcloud beta services mcp enable stitch.googleapis.com
```

Config MCP :
```json
{
  "mcpServers": {
    "stitch": {
      "command": "npx",
      "args": ["-y", "stitch-mcp"],
      "env": {
        "GOOGLE_CLOUD_PROJECT": "YOUR_PROJECT_ID"
      }
    }
  }
}
```

**Option C : Clé API directe**

```bash
export STITCH_API_KEY="your-api-key"
```

### Vérification

Tester que le MCP fonctionne :
```bash
npx @_davideast/stitch-mcp tool list_projects
```

Si le MCP n'est pas disponible, informer l'utilisateur et proposer :
1. L'aider à installer Stitch MCP
2. Utiliser Pencil à la place (si disponible)
3. Continuer sans maquette et aller directement sur Webflow

## Outils MCP Stitch disponibles

| Outil | Description |
|-------|-------------|
| `create_project` | Créer un nouveau projet Stitch |
| `list_projects` | Lister tous les projets |
| `get_project` | Détails d'un projet |
| `list_screens` | Lister les écrans d'un projet |
| `get_screen` | Métadonnées d'un écran |
| `generate_screen_from_text` | Générer un écran depuis un prompt texte |
| `extract_design_context` | Extraire la DA d'un écran (fonts, couleurs, layouts) |
| `fetch_screen_code` | Télécharger le HTML/CSS d'un écran |
| `fetch_screen_image` | Télécharger le screenshot d'un écran |

### Outils virtuels (via @_davideast/stitch-mcp proxy)

| Outil | Description |
|-------|-------------|
| `build_site` | Construire un site en mappant les écrans à des routes |
| `get_screen_code` | Récupérer le HTML d'un écran |
| `get_screen_image` | Récupérer le screenshot en base64 |

## Checklist

### Phase 1 : Préparation

- [ ] 1. Lire les 4 documents de design :
         - `~/.claude/webflow/{project}/prd.md`
         - `~/.claude/webflow/{project}/style-guide.md`
         - `~/.claude/webflow/{project}/sitemap.md`
         - `~/.claude/webflow/{project}/copy-deck.md`
- [ ] 2. Identifier les pages à maquetter (depuis le sitemap)
- [ ] 3. Demander à l'utilisateur quelles pages en priorité
         (proposer : "Toutes" ou lister par priorité P0 → P1 → P2)

### Phase 2 : Créer le projet Stitch

- [ ] 4. Créer un projet Stitch : `create_project` avec le nom du projet
- [ ] 5. Générer le premier écran (page la plus importante, souvent la Home) :
         - Construire un prompt détaillé à partir du style-guide + sitemap + copy-deck
         - Le prompt doit inclure : layout, couleurs (hex), fonts, textes réels, CTA
         - Appeler `generate_screen_from_text` avec ce prompt
- [ ] 6. Récupérer le screenshot : `fetch_screen_image`
- [ ] 7. Montrer le screenshot à l'utilisateur pour validation

### Phase 3 : Extraire et réutiliser la DA

- [ ] 8. Extraire le design context du premier écran validé :
         `extract_design_context` → récupérer fonts, couleurs, layouts
- [ ] 9. Pour chaque écran suivant, utiliser le "Designer Flow" :
         a. Inclure le design context extrait dans le prompt
         b. Appeler `generate_screen_from_text` avec le context + les specs de la page
         c. Récupérer le screenshot et faire valider
- [ ] 10. Répéter pour chaque page du sitemap

### Phase 4 : Export et documentation

- [ ] 11. Pour chaque écran validé, récupérer le code :
          `fetch_screen_code` → sauvegarder le HTML/CSS
- [ ] 12. Sauvegarder les screenshots dans le dossier du projet :
          `~/.claude/webflow/{project}/mockups/`
- [ ] 13. Sauvegarder le HTML/CSS pour référence :
          `~/.claude/webflow/{project}/mockups/{page-name}.html`
- [ ] 14. Si le wrapper @_davideast est installé, proposer de build un site preview :
          `build_site` avec les routes mappées
- [ ] 15. Annoncer que les maquettes sont prêtes et proposer :
          "Les maquettes sont prêtes. On passe à la construction sur Webflow ?"

## Comment écrire un bon prompt Stitch

### Structure du prompt

```
Crée un écran {type} pour {nom du projet}.

STYLE :
- Couleur primaire : {hex}
- Couleur secondaire : {hex}
- Fond : {hex}
- Font titres : {font}
- Font corps : {font}
- Style : {minimaliste/bold/corporate/créatif}

LAYOUT :
- {description de la structure section par section}

CONTENU :
- Titre : "{texte exact du H1}"
- Sous-titre : "{texte exact}"
- CTA : "{texte du bouton}" → lien vers {url}
- Section features : {liste des features avec titres et descriptions}
...

CONTRAINTES :
- Desktop 1440px
- Responsive mobile 390px
- {contraintes spécifiques}
```

### Le "Designer Flow" (2 étapes)

Pour garder la cohérence entre les écrans :

1. **Extraire** : `extract_design_context` sur le premier écran validé
   → Récupère les fonts, couleurs, layouts, spacing
2. **Générer** : `generate_screen_from_text` avec le context + prompt de la nouvelle page
   → Le nouvel écran respecte la DA du premier

Toujours utiliser ce flow à partir du 2e écran.

## Erreurs courantes à éviter

| Erreur | Correction |
|--------|-----------|
| Prompt vague ("fais une landing page") | Inclure couleurs hex, fonts, textes réels |
| Ignorer le design context | Toujours extraire et réutiliser entre les écrans |
| Ne pas valider chaque écran | Montrer le screenshot et faire valider avant de continuer |
| Oublier de sauvegarder le HTML | Toujours `fetch_screen_code` pour référence |
| Générer sans les docs de design | Lire PRD + style guide + sitemap + copy deck d'abord |
| Ne pas proposer le preview local | Utiliser `serve` ou `build_site` si le wrapper est installé |
