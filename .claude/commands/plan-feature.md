# /plan-feature — cadrer avant de coder

Utilise cette commande pour une feature non triviale ou une refacto qui peut toucher plusieurs couches.

## Procédure
1. Lire `spec.md`, `architecture.md`, `roadmap.md`, `CLAUDE.md` et `DESIGN.md` si l'UI est concernée.
2. Appeler `@feature-planner` avec le besoin, les fichiers concernés et les zones d'incertitude.
3. Produire un plan court en tranches verticales : métier/API/données/UI/tests/e2e/docs/ops selon le besoin.
4. Définir les preuves minimales avant de coder.
5. Mettre à jour `roadmap.md` ou un feature brief seulement si le périmètre est clair.

## Interdits
- Ne pas coder pendant cette commande.
- Ne pas inventer d'exigence absente des docs ou de la demande humaine.
- Ne pas faire un plan lourd pour une tâche simple.
