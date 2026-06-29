# CLAUDE.md

## The Mission

Memory is identity. When an AI forgets everything between conversations, it cannot build real understanding — of you, your work, your people, your life.

MemPalace exists to solve this. It is a memory system — not a search engine, not a RAG pipeline, not a vector database wrapper. It treats every word you have shared as sacred, stores it verbatim, and makes it instantly available. Your data never leaves your machine. We never summarize. We never paraphrase. We return your exact words.

100% recall is the design requirement — the target every search path is measured against. Anything less means forgetting, and forgetting means starting over.

The name comes from the ancient "method of loci" — the memory palace technique used for thousands of years to organize and recall vast amounts of information by placing it in imagined rooms of an imagined building. We were also inspired by the Zettelkasten method (created by German sociologist Niklas Luhmann) — small cross-referenced index cards that point to each other. We apply both ideas to AI memory:

- **Wings** for broad categories (people, projects, topics)
- **Rooms** for time-based groupings (days, sessions)
- **Drawers** for full verbatim content (your exact words)
- **AAAK compression** for the index layer — a compact symbolic format (via `dialect.py`) that lets an LLM scan thousands of entries instantly and know exactly which drawer to open

## Design Principles

These are non-negotiable. Every PR, every feature, every refactor must honor them.

- **Verbatim always** — Never summarize, paraphrase, or lossy-compress user data. The system searches the index and returns the original words. If a user said it, we store exactly what they said. This is the foundational promise.
- **Incremental only** — Append-only ingest after initial build. Never destroy existing data to rebuild. A crash mid-operation must leave the existing palace untouched.
- **Entity-first** — Everything is keyed by real names with disambiguation by DOB, ID, or context. People matter more than topics.
- **Local-first, zero API** — All extraction, chunking, and embedding happens on the user's machine. No cloud dependency for memory operations. No API keys required.
- **Performance budgets** — Hooks under 500ms. Startup injection under 100ms. Memory should feel instant.
- **Privacy by architecture** — The system physically cannot send your data because it never leaves your machine. No telemetry, no phone-home, no external service dependencies for core operations.
- **Background everything** — Filing, indexing, timestamps, and pipeline work happen via hooks in the background. Nothing interrupts the user's conversation. Zero tokens spent on bookkeeping in the chat window.

## Contributing

We welcome bug fixes, performance improvements, new language support, better entity disambiguation, documentation, and test coverage.

We do not accept summarization of user content, cloud storage/sync features, telemetry or analytics, features requiring API keys for core memory, or shortcuts that bypass verbatim storage.

## Setup

```bash
pip install -e ".[dev]"
```

## Commands

```bash
# Run tests
python -m pytest tests/ -v --ignore=tests/benchmarks

# Run tests with coverage
python -m pytest tests/ -v --ignore=tests/benchmarks --cov=mempalace --cov-report=term-missing

# Lint
ruff check .

# Format
ruff format .

# Format check (CI mode)
ruff format --check .
```

## Project Structure

```
mempalace/
├── mcp_server.py        # MCP server — all read/write tools
├── cli.py               # CLI dispatcher
├── config.py            # Configuration + input validation
├── miner.py             # Project file miner
├── convo_miner.py       # Conversation transcript miner
├── searcher.py          # Semantic search (hybrid BM25 + vector)
├── knowledge_graph.py   # Temporal entity-relationship graph (SQLite)
├── palace.py            # Shared palace operations
├── palace_graph.py      # Room traversal + cross-wing tunnels
├── backends/            # Pluggable storage backends (ChromaDB default)
│   ├── base.py          # Abstract interface — implement this for new backends
│   └── chroma.py        # ChromaDB implementation
├── dialect.py           # AAAK compression dialect
├── normalize.py         # Transcript format detection + normalization
├── entity_detector.py   # Auto-detect people/projects from content
├── entity_registry.py   # Entity storage and disambiguation
├── layers.py            # L0-L3 memory wake-up stack
├── onboarding.py        # Interactive first-run setup
├── repair.py            # Palace repair and consistency checks
├── dedup.py             # Deduplication
├── migrate.py           # ChromaDB version migration
├── spellcheck.py        # Auto-correct user messages
├── exporter.py          # Palace data export
├── hooks_cli.py         # Hook management CLI
├── query_sanitizer.py   # Prompt contamination prevention
├── split_mega_files.py  # Split concatenated transcript files
└── version.py           # Single source of truth for version

hooks/                   # Claude Code hook scripts
├── mempal_save_hook.sh        # Stop: triggers diary save
└── mempal_precompact_hook.sh  # PreCompact: saves state before compression
```

## Conventions

- **Python style**: snake_case for functions/variables, PascalCase for classes
- **Linter**: ruff with E/F/W rules
- **Formatter**: ruff format, double quotes
- **Commits**: conventional commits (`fix:`, `feat:`, `test:`, `docs:`, `ci:`)
- **Tests**: `tests/test_*.py`, fixtures in `tests/conftest.py`
- **Coverage**: 85% threshold (80% on Windows due to ChromaDB file lock cleanup)

## Architecture

```
User → CLI / MCP Server → Storage Backend (ChromaDB default, pluggable)
                        → SQLite (knowledge graph)

Palace structure:
  WING (person/project)
    └── ROOM (day/topic)
          └── DRAWER (verbatim text chunk)

Index layer (AAAK):
  Compressed pointers → DRAWER locations
  Scanned by LLM to find relevant drawers without reading all content

Knowledge Graph:
  ENTITY → PREDICATE → ENTITY (with valid_from / valid_to dates)
```

## Key Files for Common Tasks

- **Adding an MCP tool**: `mempalace/mcp_server.py` — add handler function + TOOLS dict entry
- **Changing search**: `mempalace/searcher.py`
- **Modifying mining**: `mempalace/miner.py` (project files) or `mempalace/convo_miner.py` (transcripts)
- **Adding a storage backend**: subclass `mempalace/backends/base.py`, register in `backends/__init__.py`
- **Input validation**: `mempalace/config.py` — `sanitize_name()` / `sanitize_content()`
- **Tests**: mirror source structure in `tests/test_<module>.py`

## Correction Memory Rule

Si une correction utilisateur révèle une règle durable **propre à ce projet**, proposer de l'ajouter dans `CLAUDE.md` sous forme de pitfall actionnable : symptôme, règle, vérification. Ne pas modifier `CLAUDE.md` pour une préférence temporaire, un contexte de session ou une erreur déjà couverte ailleurs.
---

## Agentic Delivery Layer

Utiliser `.claude/agents/` et `.claude/commands/` comme couche d'exécution spécialisée. Agents disponibles :

- `@feature-planner` → feature/refacto non triviale, cadrage, feature brief, découpage et preuves attendues
- `@debugger` → bug, régression, comportement incohérent, cause racine à isoler
- `@test-engineer` → manque de couverture unit / integration, dette de preuve, non-régression
- `@playwright-e2e` → parcours critique visible, golden path, test flaky, setup e2e
- `@frontend-designer` → nouvel écran, composant, responsive, accessibilité minimale, conformité `DESIGN.md`
- `@devops-ci` → Docker, CI, scripts, env, build, déploiement, diagnostic runner/config
- `@code-reviewer` → revue avant merge ou avant livraison importante
- `@product-guardian` → contrôle de conformité à `spec.md`, `architecture.md`, `roadmap.md`, `DESIGN.md` (si présent)
- `@security-review` → auth, cookies, secrets, uploads, webhooks, permissions, providers IA et exposition de données
- `@docs-maintainer` → synchronisation `README.md`, `spec.md`, `architecture.md`, `roadmap.md`, `.env.example`, `DEPLOY.md`

Commandes utiles : `/plan-feature`, `/review-ready`, `/ship-check`.

---

---

## Mode opératoire Claude Code

Pour une tâche complexe, appliquer le pattern **Plan → Execute → Review → Ship** :

1. **Plan** : `/plan-feature` ou `@feature-planner` pour clarifier le besoin, les hypothèses, les fichiers et les preuves.
2. **Execute** : implémenter petit, par tranches vérifiables ; appeler `@frontend-designer`, `@devops-ci`, `@debugger`, `@test-engineer` ou `@playwright-e2e` uniquement si leur expertise est utile.
3. **Review** : `/review-ready`, puis `@code-reviewer`, `@product-guardian` et/ou `@security-review` selon la surface.
4. **Ship** : `/ship-check`, preuves réelles, roadmap/doc synchronisées, résumé final court.

Ne pas créer une mécanique lourde pour une tâche simple. Pour un changement trivial, appliquer seulement les étapes nécessaires, mais ne jamais supprimer la preuve minimale.

---
---

## Choisir le bon mécanisme Claude Code

- **Agent** : tâche autonome, multi-étapes, avec contexte isolé et expertise claire.
- **Commande** : workflow déclenché explicitement par l'humain (`/plan-feature`, `/review-ready`, `/ship-check`).
- **Règle / doc** : convention stable que tous les agents doivent respecter.
- Ne pas créer un agent pour une simple checklist ou une règle courte.
- Ne pas appeler un agent expert comme décoration : lui donner objectif, fichiers, contraintes et preuve attendue.

---

## Gestion du contexte

- Ne pas charger tout le repo par défaut.
- Lire uniquement les fichiers nécessaires à la tâche.
- Pour une grosse tâche, découper en sous-tâches vérifiables.
- Pour une exploration sans modification, préférer `@feature-planner`, `@product-guardian` ou un agent read-only.
- Si le contexte devient confus ou contradictoire, résumer l'état réel, recharger les fichiers sources et reprendre depuis les preuves.

## Pitfall — Attendre une CI GitHub verte (polling)

**Contexte** : CI **GitHub Actions** de ce repo — deux checks par PR : `ci`
(lint/typecheck/unit) et `docker-build` (image + smoke). GitHub uniquement
via l'outil MCP (`mcp__github__*`), pas de `gh` CLI ni de token en bash.

**Symptôme** : on « attend que la CI passe au vert » mais on tombe toujours
juste avant la fin (« `ci` vert, `docker-build` en cours… ») et on re-programme
un réveil long → impression d'attente sans fin, et la session dort pour rien.

**Cause** : les webhooks GitHub ne notifient QUE les échecs CI et les reviews —
**jamais un succès**. Entre deux tours la session est dormante : le seul moyen
de constater un « vert » est un réveil qu'on a soi-même armé. Un intervalle fixe
long (≥ 5 min) ne se cale jamais sur une durée de build variable. On ne peut pas
faire `while !vert: sleep` (sleep avant-plan bloqué + GitHub inaccessible depuis
bash → uniquement via MCP).

**Règle** :
- Ne jamais merger sur un check partiel : attendre que **TOUS** les checks
  GitHub (`ci` **et** `docker-build`) soient `completed/success`.
- Pour attendre un vert : **réveils courts (~45–60 s) répétés jusqu'au vert**,
  pas un gros intervalle fixe ; resserrer près de la fin estimée du build.
- `docker-build` ≈ 2–3 min après `ci` : attendre cette durée **puis** sonder
  court — ne pas sonder pile à la durée estimée.
- Annoncer « la CI GitHub tourne ~N min, je reviens quand c'est vert » plutôt
  que d'égrener des « toujours en cours ».

**Vérification** : un seul appel `mcp__github__pull_request_read`
(`get_check_runs`) montre les deux checks en `completed/success` avant merge ;
peu de cycles d'attente (pas 4+ réveils qui ratent la fin de peu).
