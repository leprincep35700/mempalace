# /review-ready — vérifier avant review humaine

Utilise cette commande avant PR, merge ou livraison importante.

## Procédure
1. Inspecter `git status --short --branch` et le diff.
2. Vérifier les docs structurantes impactées : `README.md`, `spec.md`, `architecture.md`, `roadmap.md`, `DEPLOY.md`, `.env.example`.
3. Appeler les agents adaptés :
   - `@code-reviewer` pour le diff global ;
   - `@product-guardian` si le produit, la roadmap ou `DESIGN.md` changent ;
   - `@security-review` si auth, secrets, cookies, webhooks, permissions, providers IA ou données sensibles changent ;
   - `@docs-maintainer` si les docs sont incohérentes.
4. Lancer les commandes de preuve pertinentes : lint, typecheck, unit, build, e2e, smoke, Docker/CI selon le changement.
5. Corriger les blocants avant de demander une review humaine.

## Sortie attendue
- statut du diff ;
- preuves lancées ;
- findings bloquants ou absence de findings ;
- dette restante assumée.
