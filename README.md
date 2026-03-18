# webflow-builder

Plugin Claude Code pour créer des sites Webflow de A à Z — du brainstorm à la publication, en passant par la maquette.

## Le flow complet

```
1. Brainstorm        → Créer le PRD, style guide, sitemap, copy deck
2. Maquette Pencil   → Wireframe + maquette stylée (recommandé, optionnel)
3. Build Webflow     → Construire le site en respectant la maquette et la DA
4. Audit + Publish   → Vérifier et publier (via skills officiels Webflow)
```

---

## Installation

### Étape 1 : Installer ce plugin

```bash
# UNE commande, c'est tout
git clone https://github.com/ohvignas/webflow-builder.git ~/.claude/skills/webflow-builder
```

C'est tout. Les skills sont automatiquement détectés par Claude Code au prochain lancement.

### Étape 2 : Installer le MCP Webflow (obligatoire)

```bash
claude mcp add --transport http webflow https://mcp.webflow.com/mcp
```

Suivre les instructions d'authentification quand Claude le demande.
Doc complète : https://developers.webflow.com/mcp

### Étape 3 : Installer les skills officiels Webflow (recommandé)

Les skills officiels ajoutent site-audit, safe-publish, link-checker, accessibility-audit, etc.

```bash
claude plugin marketplace add webflow/webflow-skills
claude plugin install webflow-skills@webflow-skills
```

Repo : https://github.com/webflow/webflow-skills

### Étape 4 : Installer Pencil (recommandé)

Pencil permet de créer des maquettes visuelles avant de construire sur Webflow.

1. Ouvrir VS Code ou Cursor
2. Aller dans Extensions → chercher "Pencil"
3. Installer l'extension
4. Le MCP Pencil est inclus automatiquement — pas de config supplémentaire

### Vérification

Relancer Claude Code, puis vérifier que tout est détecté :

```
# Dans Claude Code, taper :
/webflow-new-project
```

Si le skill se lance, c'est bon. Sinon, vérifier que le dossier est bien dans `~/.claude/skills/webflow-builder/`.

---

## Utilisation

### Comment lancer les skills

Les skills se déclenchent de deux façons :

**1. Par slash command (direct) :**
```
/webflow-new-project     → Lancer un nouveau projet from scratch
/webflow-build-page      → Construire une nouvelle page
/webflow-discovery        → Scanner un site existant
/pencil-mockup            → Créer une maquette Pencil
```

**2. En langage naturel (auto-détecté) :**
```
"Je veux créer un nouveau site web"           → webflow-new-project
"Ajoute une page pricing à mon site"          → webflow-build-page
"Fais-moi une maquette de la homepage"        → pencil-mockup
"Scanne mon site Webflow"                     → webflow-discovery
"Audite mon site" / "Publie le site"          → skills officiels Webflow
```

### Quel skill utiliser ?

| Tu veux... | Skill à utiliser |
|------------|-----------------|
| Créer un site from scratch | `/webflow-new-project` |
| Ajouter une page à un site existant | `/webflow-build-page` |
| Faire une maquette visuelle | `/pencil-mockup` |
| Scanner un site avant de le modifier | `/webflow-discovery` |
| Auditer ton site (SEO, liens, accessibilité) | Skills officiels Webflow |
| Publier ton site | `/safe-publish` (skill officiel) |

### Premier lancement — nouveau projet

```
Toi : /webflow-new-project

Claude : "On va brainstormer ensemble. C'est quoi le nom de ton projet ?"
Toi : "ILLITH School"
Claude : "Quel est l'objectif principal du site ?"
Toi : "Générer des leads pour le bootcamp"
...
(Questions une par une → 4 documents créés → maquette proposée → build Webflow)
```

---

## Skills de ce plugin

| Skill | Description |
|-------|-------------|
| `webflow-new-project` | Brainstorm complet → 4 docs de design → maquette Pencil → build Webflow |
| `pencil-mockup` | Créer des maquettes .pen (wireframe + version stylée) depuis les docs de design |
| `webflow-build-page` | Construire une page Webflow avec plan validé + maquette optionnelle |
| `webflow-discovery` | Scanner un site existant et documenter sa DA, composants et patterns |

## Workflows par cas d'usage

### Nouveau site from scratch
```
webflow-new-project
  ├── Brainstorm (questions 1 par 1)
  ├── Création des 4 docs (PRD, style guide, sitemap, copy deck)
  ├── pencil-mockup (wireframe → maquette stylée)
  ├── Setup Webflow
  ├── webflow-discovery
  ├── webflow-build-page (pour chaque page)
  ├── accessibility-audit ← skill officiel
  ├── link-checker ← skill officiel
  └── safe-publish ← skill officiel
```

### Nouvelle page sur site existant
```
webflow-discovery (si pas déjà fait)
  └── webflow-build-page
        ├── Mini docs de design pour la page
        ├── pencil-mockup (recommandé)
        ├── Plan de page validé
        ├── Construction section par section
        └── Audit + publish
```

### Maintenance / audit
```
site-audit ← skill officiel
link-checker ← skill officiel
accessibility-audit ← skill officiel
asset-audit ← skill officiel
safe-publish ← skill officiel
```

## Documents générés

```
~/.claude/webflow/{project-name}/
├── prd.md              ← Identité, cible, objectif, conversion
├── style-guide.md      ← Couleurs, fonts, espacements, composants
├── sitemap.md          ← Pages + sections détaillées + SEO
├── copy-deck.md        ← Textes réels pour chaque section
├── mockups/            ← Maquettes Pencil
│   ├── home.pen
│   ├── home.png
│   └── ...
└── discovery/          ← Docs auto-générés depuis Webflow
    ├── discovery.md
    ├── components.md
    ├── styles.md
    └── pages.md
```

## Complémentarité

| Ce plugin | Skills officiels Webflow | Pencil MCP |
|-----------|------------------------|------------|
| Brainstorm + docs de design | — | — |
| — | — | Maquettes visuelles |
| Discovery DA site | Audit santé site | — |
| Construction guidée | Publication sécurisée | — |
| — | Audit accessibilité, liens, assets | — |
