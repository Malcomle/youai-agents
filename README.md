# youai-agents

Le système d'agents youai : 3 rôles fonctionnels, 9 skills, 2 conventions. Portable — ces fichiers marchent dans Paperclip, Claude Code, ou n'importe quel harness futur. **Aucun concept d'entreprise** : pas de CEO, pas d'organigramme, pas de méta-travail. L'outil d'orchestration n'est qu'un écran de contrôle (tickets, coûts, sessions) branché sur ce process.

## Structure

```
conventions/   Les règles transverses, référencées par tous les rôles
  definition-of-done.md    ce qu'est un ticket terminé (tests verts + PR + preuve)
  stop-rules.md            anti-busywork : interdictions + quand escalader
roles/         Les fiches de poste (à coller dans le prompt de chaque agent)
  dispatcher.md   demande humaine → tickets briefés ; seul contact humain
  dev.md          un ticket → une PR, puis stop ; ne merge jamais
  qa.md           vérifie les PR contre les critères ; ne corrige jamais
skills/        Les playbooks (à assigner par rôle, jamais tous à tout le monde)
  to-spec, to-tickets, triage          → dispatcher
  implement, tdd, diagnosing-bugs,
  resolving-merge-conflicts, pr        → dev
  code-review                          → qa
```

## Installation dans Paperclip

1. **Importer les skills** : Skills Store → Import from GitHub → ce repo (épinglé sur un commit SHA). Réimporter après chaque mise à jour du repo.
2. **Créer les 3 agents** (adapter `claude_local`) : le prompt de chaque agent = le contenu de son fichier `roles/*.md` + les deux fichiers de `conventions/` (ou une référence si les conventions sont importées comme skill).
3. **Assigner les skills par rôle** — uniquement ceux listés en bas de chaque fiche. Un agent avec trop de skills divague et brûle des tokens.
4. **Budgets** : hard-stop par agent dès le premier jour. Dispatcher et QA sur Sonnet (ils tournent souvent), dev sur Opus si besoin.

## Principes (résumé)

- La valeur durable est ici, en markdown versionné — pas dans la config de l'outil d'orchestration.
- Chaque ticket a un livrable vérifiable. Le journal d'activité n'est pas un livrable.
- Les humains gardent les deux pouvoirs non délégables : parler au dispatcher, merger les PR.
- Deux allers-retours max entre agents sur un même sujet, ensuite escalade humaine.
