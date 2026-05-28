# /ship-check — preuve finale de livraison

Utilise cette commande avant de dire qu'une feature, un bugfix ou un déploiement est terminé.

## Procédure
1. Relire la définition de terminé dans `CLAUDE.md` et `AGENTS.md`.
2. Vérifier que la roadmap reflète l'état réel.
3. Lancer la plus petite suite de preuve suffisante puis, si nécessaire, la suite large :
   - `pnpm ci:checks`
   - `pnpm build`
   - `pnpm test:e2e` ou `pnpm smoke` pour parcours critique
   - commandes Docker/CI spécifiques si le changement touche l'exploitation
4. Si une preuve est impossible, documenter explicitement la dette et ne pas dire “done” sans nuance.
5. Préparer un résumé final court : ce qui a changé + prochaine étape.

## Interdits
- Ne pas considérer HTTP 200 seul comme preuve UI.
- Ne pas considérer un fichier généré comme preuve media/audio sans vérifier taille/type/décodage si pertinent.
- Ne pas ignorer une CI rouge non comprise.
