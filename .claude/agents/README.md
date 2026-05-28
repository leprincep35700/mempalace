# Claude Agents

Ce dossier ajoute une couche d'exécution spécialisée pour le projet.

Le principe :
- `AGENTS.md` = règles communes pour tout agent
- `CLAUDE.md` = contrat principal du repo
- `.claude/agents/*.md` = experts activables à la demande dans Claude Code
- `.claude/commands/*.md` = workflows déclenchés explicitement par l'humain

## Choisir le bon agent

| Besoin | Agent |
|---|---|
| Cadrer une feature ou refacto avant de coder | `@feature-planner` |
| Bug / régression / cause racine | `@debugger` |
| Tests unitaires ou intégration | `@test-engineer` |
| Parcours visible critique / e2e flaky | `@playwright-e2e` |
| UI, responsive, accessibilité minimale, DESIGN.md | `@frontend-designer` |
| Docker, CI, env, build, déploiement | `@devops-ci` |
| Cohérence produit / architecture / roadmap | `@product-guardian` |
| Sécurité / données sensibles / auth / webhooks | `@security-review` |
| Revue finale avant merge/livraison | `@code-reviewer` |
| Synchronisation documentaire | `@docs-maintainer` |

## Agents inclus

### `@feature-planner`
À utiliser pour :
- préparer une feature non triviale ;
- cadrer une refacto ;
- découper le travail en tranches vérifiables ;
- définir les preuves attendues avant codage ;
- éviter les hypothèses silencieuses.

### `@debugger`
À utiliser pour :
- reproduire un bug ;
- isoler la cause racine ;
- proposer puis appliquer un correctif minimal ;
- ajouter le test de non-régression adapté ;
- remettre à jour `docs/bugs/BUGS.md` quand le bug est fixé.

### `@test-engineer`
À utiliser pour :
- compléter la stratégie de preuve ;
- écrire / renforcer tests unitaires et d'intégration ;
- combler les trous de couverture utiles ;
- réduire les tests fragiles ou redondants.

### `@playwright-e2e`
À utiliser pour :
- créer ou corriger des scénarios Playwright ;
- stabiliser un golden path ;
- améliorer les assertions utilisateur ;
- diagnostiquer un test e2e flaky.

### `@frontend-designer`
À utiliser pour :
- créer ou améliorer une interface ;
- vérifier la conformité à `DESIGN.md` ;
- traiter responsive, états utilisateur et accessibilité minimale ;
- rendre les écrans testables via Playwright.

### `@devops-ci`
À utiliser pour :
- diagnostiquer une CI rouge ;
- corriger Docker Compose, Dockerfile, scripts ou workflows ;
- vérifier `.env.example`, `DEPLOY.md`, healthchecks et commandes documentées ;
- distinguer panne code/config/environnement.

### `@code-reviewer`
À utiliser pour :
- revue avant merge ;
- recherche de bugs, dette, risques sécurité, oublis de tests ;
- vérification de cohérence entre code, roadmap et doc.

### `@product-guardian`
À utiliser pour :
- vérifier l'alignement avec `spec.md`, `architecture.md` et `roadmap.md` ;
- détecter le scope creep ;
- rappeler les manques de preuve de finition ;
- challenger une feature "faite" mais pas vraiment terminée.

### `@security-review`
À utiliser pour :
- revue ciblée sécurité avant merge d'une feature sensible ;
- auth / sessions / cookies / reverse proxy ;
- secrets / variables d'environnement / permissions ;
- uploads / webhooks / providers externes / exposition de données ;
- validation de garde-fous avant une livraison sensible.

### `@docs-maintainer`
À utiliser pour :
- synchroniser README, spec, architecture, roadmap, `.env.example` et DEPLOY ;
- nettoyer les incohérences documentaires après une implémentation ;
- rendre la documentation cohérente avant review sans inventer de décisions.

## Commandes incluses

- `/plan-feature` — cadrage avant codage avec `@feature-planner`.
- `/review-ready` — revue diff/docs/preuves avant review humaine ou PR.
- `/ship-check` — preuve finale avant de dire qu'un sujet est terminé.

## Règles d'utilisation

1. Toujours lire le contexte minimal utile : `spec.md`, `architecture.md`, `roadmap.md`, `CLAUDE.md`, puis `DESIGN.md` si UI.
2. N'invoquer un agent expert qu'avec un objectif clair, les fichiers utiles et le niveau de preuve attendu.
3. Pour une feature non triviale : commencer par `@feature-planner`, puis déléguer aux spécialistes.
4. Pour un bug : commencer par `@debugger`, puis passer à `@test-engineer` ou `@playwright-e2e` si nécessaire.
5. Pour un parcours critique visible : `@frontend-designer` et `@playwright-e2e` doivent intervenir tôt, pas seulement à la fin.
6. Pour Docker/CI/déploiement : `@devops-ci` doit diagnostiquer sans masquer les erreurs.
7. Pour auth / secrets / cookies / providers / webhooks : `@security-review` doit intervenir avant de déclarer la livraison propre.
8. Avant de déclarer une tâche finie : faire passer `@code-reviewer` ou `@product-guardian` sur le résultat.
9. Après changement structurant : utiliser `@docs-maintainer` pour synchroniser la doc.

## Exemples de prompts

- `@feature-planner Cadre cette feature avant implémentation : objectifs, hypothèses, découpage, preuves attendues.`
- `@debugger Reproduis le bug de création de devis, trouve la cause racine, corrige-le, ajoute le test de non-régression et mets à jour BUGS.md.`
- `@frontend-designer Revois cet écran selon DESIGN.md : responsive, états vides/erreur, accessibilité minimale et testabilité Playwright.`
- `@devops-ci Diagnostique cette CI rouge, distingue code/config/environnement, corrige sans masquer les erreurs.`
- `@test-engineer Renforce les tests autour de l'auth, sans dupliquer inutilement les cas existants.`
- `@playwright-e2e Crée le golden path de signup puis vérifie qu'il tourne contre la stack Docker locale.`
- `@code-reviewer Review les changements de cette branche comme avant une PR critique.`
- `@product-guardian Vérifie si cette feature est vraiment conforme à spec.md et à la roadmap.`
- `@security-review Passe en revue les cookies, secrets, callbacks OAuth, permissions et webhooks de cette implémentation.`
- `@docs-maintainer Synchronise README, architecture.md, roadmap.md, .env.example et DEPLOY.md avec cette branche.`
