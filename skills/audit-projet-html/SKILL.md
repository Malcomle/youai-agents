---
name: audit-projet-html
description: >
  Produire un audit technique complet d'un projet logiciel et le livrer sous forme
  d'un fichier HTML autonome d'une seule page (sidebar de navigation, score global
  et scores par domaine, findings sévérisés, tableaux d'inventaire, roadmap
  priorisée P0→P3). À utiliser dès qu'on demande « un audit » d'un dépôt ou d'un
  projet, « le même audit que trendy-food », « régénère l'Audit_Complet pour X »,
  ou un rapport d'audit HTML au format maison.
---

# Audit projet → rapport HTML

Cette skill décrit **la méthode d'audit** et **le format de livrable** utilisés par
youAI. Le livrable est toujours le même : un seul fichier `.html` autonome,
ouvrable par double-clic, sans build ni serveur, lisible par une direction IT.

Référence d'origine du format : `Audit_Complet_v2_trendy-food-main.html`.

## 1. Livrable

- **Un seul fichier** : `Audit_Complet_<version>_<nom-projet>.html`
  (ex. `Audit_Complet_v1_mon-app.html`). Pas de CSS/JS externe, pas d'images
  locales. Seule dépendance réseau tolérée : la police Outfit via Google Fonts
  (le rapport reste lisible sans réseau grâce au fallback `sans-serif`).
- **Langue** : français. Ton factuel, orienté décision. Pas de jargon inutile
  dans la synthèse exécutive — elle est lue par des non-développeurs.
- **Taille cible** : 60 à 120 Ko, 15 à 35 findings. Un audit qui tient en
  10 findings est un audit bâclé ; au-delà de 40, regrouper.
- Tu ne livres **jamais** un rapport dont les chiffres n'ont pas été mesurés sur
  le code (voir §3).

## 2. Procédure

Cinq phases, dans cet ordre. Ne saute pas la phase 2 : chaque chiffre affiché
dans le rapport doit venir d'une commande réellement exécutée.

### Phase 1 — Cadrage

1. Localiser le dépôt (chemin local, URL git, ou workspace de la tâche).
2. Lire à la racine : `README*`, `package.json` / `pyproject.toml` / `go.mod` /
   `composer.json`, `CLAUDE.md` / `AGENTS.md`, fichiers de config
   (`next.config.*`, `tsconfig.json`, `docker-compose.yml`, `Dockerfile`,
   `ecosystem.config.*`, CI).
3. En déduire : stack, gestionnaire de paquets, framework, base de données,
   présence d'un pipeline IA, mode de déploiement.
4. Décider la **liste des sections** du rapport (voir §4) : les 8 sections
   socle sont obligatoires, les sections conditionnelles ne sont incluses que si
   le projet a la brique correspondante.

### Phase 2 — Collecte mesurée

Exécuter `scripts/collect-metrics.sh <chemin-du-projet>` (ou les commandes
équivalentes listées dans `references/collecte.md` si l'environnement n'a pas
bash). Elle produit les compteurs bruts : nombre de fichiers, lignes de code,
`console.log`, `any`, `TODO`/`FIXME`, `@ts-ignore`/`@ts-nocheck`, routes API,
secrets potentiels, dépendances à risque.

Puis, lecture ciblée obligatoire :

- **toutes** les routes API / contrôleurs : présence et niveau d'authentification,
  validation des entrées, bornes de pagination ;
- le schéma de base de données en entier ;
- les points d'entrée des jobs/crons/workers ;
- les fichiers de config de build et de déploiement ;
- si pipeline LLM : orchestrateur, sous-agents, schémas de sortie, prompts.

### Phase 3 — Analyse par catégorie

Pour chaque section retenue, appliquer la grille de `references/categories.md`.
Chaque constat devient un **finding** (§5) ou une ligne de tableau d'inventaire.

### Phase 4 — Scoring

Calculer un score par domaine et le score global selon `references/scoring.md`.
Les scores doivent être cohérents avec les findings : un domaine avec un finding
critique ne peut pas dépasser 60.

### Phase 5 — Génération et livraison

1. Copier `references/report-template.html` comme base.
2. Remplacer les placeholders `{{...}}` (liste en tête du template).
3. Construire chaque section avec les blocs de `references/composants.md`.
4. Passer la checklist §7.
5. Livrer (§8).

## 3. Règles de véracité

Ces règles sont non négociables — c'est ce qui distingue cet audit d'un texte
générique.

- **Chaque finding cite un emplacement réel** : `chemin/fichier.ts — ligne N`
  ou `— lignes N-M`. Vérifie la ligne avant de l'écrire.
- **Chaque chiffre est mesuré.** Si une métrique n'a pas pu être mesurée,
  écris `non mesuré` — n'invente pas.
- **Jamais de finding spéculatif présenté comme certain.** Si tu n'as pas pu
  vérifier, sévérité `Info` et formulation explicite (« à vérifier », « selon
  l'implémentation de X »).
- **Chaque finding non-positif a un `finding-fix`** : action concrète, pas
  « améliorer la qualité ». Nomme le fichier, la fonction, la librairie.
- **Extrait de code** (`code-block`) obligatoire pour tout finding critique ou
  haute sévérité dont le problème est lisible en moins de 12 lignes.
- **Positifs obligatoires** : au moins 4 findings `Positif`. Un audit qui ne
  relève que des problèmes n'est pas crédible et ne montre pas ce qu'il faut
  préserver.
- **Cherche le mécanisme compensatoire avant d'écrire le finding.** Un motif
  qui *paraît* dangereux est souvent déjà couvert ailleurs dans le dépôt. Avant
  de fixer une sévérité, va vérifier l'existence du garde-fou :

  | Motif observé | Garde-fou à chercher avant de conclure |
  |---|---|
  | Conteneur sans instruction `USER` | abandon de privilèges au point d'entrée (`gosu`, `su-exec`, `setpriv`) |
  | Mode de déploiement permissif par défaut | refus de démarrage sur liaison réseau non locale |
  | État mutable en mémoire dans un orchestrateur | bail en base, élection de contrôleur, verrou consultatif, comparaison-échange |
  | Routeur monté hors de la garde globale | contrôle d'autorisation propre au routeur, ou routeur en lecture seule |
  | `dangerouslySetInnerHTML` / rendu HTML brut | assainisseur en amont et son niveau de configuration |
  | SQL construit par interpolation | validateur de confinement, liaison des paramètres, empreinte |
  | Endpoint sans garde d'authentification | publication délibérée + absence de donnée de locataire + contrôle d'admission |

  Un faux positif en sévérité haute ou critique disqualifie le rapport entier :
  le lecteur qui en trouve un cesse de faire confiance aux autres. Quand le
  garde-fou existe, le finding disparaît — et devient souvent un `Positif`,
  parce qu'un dépôt qui pose cette défense mérite qu'on le dise.
- **Ne conclus jamais depuis un chiffre global sur un monorepo.** Ventile par
  zone (`server/src`, `ui/src`, `cli/src`, `packages/*/src`) et exclus les
  tests. Un total nu mélange dette de production, code de test et sortie
  légitime de CLI : mesuré sur un dépôt réel, 601 `console.log` au total contre
  11 dans le code serveur — le total nu aurait produit un finding faux d'un
  facteur 50. Même règle pour `any`, `TODO` et les suppressions de lint.
- **Vérifie que l'outil que tu crois présent existe.** Des directives
  `eslint-disable` n'impliquent pas qu'un linter soit configuré, un dossier
  `evals/` n'implique pas que les évaluations tournent en intégration continue,
  et une suite de tests volumineuse n'implique pas qu'elle couvre le chemin
  réellement servi. Cherche la configuration et le câblage, pas l'indice.

## 4. Sections du rapport

Socle obligatoire (dans cet ordre) :

| id | icône | Titre | Contenu |
|---|---|---|---|
| `exec` | 📊 | Synthèse exécutive | 4 compteurs + 3 à 5 findings clés |
| `archi` | 🏗️ | Architecture générale | tableau stack, tableau arborescence, findings |
| `securite` | 🔒 | Sécurité | findings + tableau récapitulatif auth par route |
| `bdd` | 🗄️ | Base de données | inventaire des modèles, forts/attention, findings |
| `qualite` | ✅ | Qualité du code | grille de métriques + findings dette technique |
| `deps` | 📦 | Dépendances | tableau versions/risque/action + findings |
| `deploy` | 🚀 | Déploiement | config runtime + checklist pré-production |
| `roadmap` | 🗺️ | Roadmap des corrections | items P0→P3 avec effort estimé |

Sections conditionnelles, insérées avant `roadmap` si la brique existe :

| id | icône | Titre | Condition |
|---|---|---|---|
| `ia` | 🤖 | Pipeline IA — agents & jobs | le projet appelle un LLM |
| `prompts` | 💬 | Audit des prompts IA | les prompts sont versionnés/stockés |
| `front` | 🎨 | Frontend & accessibilité | app cliente significative |
| `tests` | 🧪 | Tests & couverture | suite de tests présente ou absente notable |
| `perf` | ⚡ | Performance | goulots mesurés |
| `obs` | 📈 | Observabilité | logs/metrics/traces |

La sidebar et l'ordre des `<div class="section" id="...">` doivent être
identiques. Détail de la grille d'analyse de chaque section :
`references/categories.md`.

## 5. Findings

Cinq sévérités, une seule signification chacune :

| Classe | Libellé | Quand |
|---|---|---|
| `finding-critical` / `sev-critical` | Critique | exploitable maintenant, en production, sans prérequis. Fuite ou altération de données. |
| `finding-high` / `sev-high` | Haute | risque réel de corruption, fuite dans les logs, ou panne ; nécessite une condition simple. |
| `finding-medium` / `sev-medium` | Moyen | dette avec impact probable : absence de borne, validation manquante, cast non sûr. |
| `finding-low` / `sev-low` | Positif | point fort à préserver. |
| `finding-info` / `sev-info` | Info | observation, choix d'architecture à connaître, piste à considérer. |

Structure d'un finding (ordre des blocs imposé) :

```html
<div class="finding finding-high">
  <div class="finding-header">
    <span class="finding-severity sev-high">Haute</span>
    <div class="finding-title">Titre court, factuel, sans « il faudrait »</div>
  </div>
  <div class="finding-body">Ce qui se passe, pourquoi c'est un problème, ce que ça coûte. 2 à 5 phrases.</div>
  <div class="finding-file">src/chemin/fichier.ts — ligne 215</div>
  <div class="code-block"><!-- preuve, coloration via .kw .str .cm .fn .err --></div>
  <div class="finding-fix"><strong>Fix :</strong> action concrète.</div>
</div>
```

`finding-file`, `code-block` et `finding-fix` sont optionnels pour un `Positif`.

## 6. Rédaction

- Titres de finding : un constat, pas une recommandation.
  ✅ « Route /api/x sans authentification » — ❌ « Ajouter l'authentification ».
- `finding-body` : nomme la conséquence métier (« n'importe qui connaissant
  l'URL peut altérer les données »), pas seulement la cause technique.
- Prefixes `finding-fix` selon l'échéance : `Fix immédiat :`, `Fix :`,
  `Fix prioritaire :`, `Recommandation :`, `À planifier :`, `À considérer :`,
  `À moyen terme :`, `Vérifier :`.
- Noms de fichiers, fonctions, variables, packages et commandes toujours dans
  `<code>`.
- Roadmap : un item = un titre d'action à l'infinitif + un paragraphe qui dit
  quoi faire et ce que ça élimine. Effort en ½ jour / 1h / 1 jour / 2-3 jours /
  1 semaine.

## 7. Checklist avant livraison

- [ ] Le fichier s'ouvre seul et s'affiche correctement (pas de balise non fermée).
- [ ] Chaque entrée de sidebar pointe vers un `id` qui existe, et inversement.
- [ ] Les 4 compteurs de la synthèse égalent le décompte réel des findings par sévérité.
- [ ] Chaque score de domaine de la sidebar a une `width:` de barre cohérente (`width = score%`).
- [ ] Score global = moyenne pondérée annoncée dans `references/scoring.md`.
- [ ] Aucun placeholder `{{...}}` restant (`grep -o '{{[^}]*}}' fichier.html` → vide).
- [ ] Chaque finding non-positif a un `finding-fix`.
- [ ] Au moins 4 findings `Positif`.
- [ ] Chaque chemin de fichier cité existe réellement dans le dépôt.
- [ ] Le `<` et `>` des extraits de code sont échappés (`&lt;` / `&gt;`).
- [ ] Chaque item de roadmap correspond à au moins un finding du rapport.
- [ ] Le script de scroll-spy est présent en fin de `<body>`.

## 8. Livraison

1. Écrire le fichier dans le dossier de travail de la tâche.
2. L'attacher à la tâche Paperclip comme artefact (voir la skill `paperclip`,
   `scripts/paperclip-upload-artifact.sh`), afin que l'utilisateur puisse le
   télécharger depuis le board.
3. Poster un commentaire court : score global, nombre de findings par sévérité,
   les 2 ou 3 actions P0, et le lien vers l'artefact. Ne recopie pas le rapport
   dans le commentaire.
4. Si l'utilisateur a donné un chemin de sortie explicite, écrire aussi le
   fichier à cet endroit.

## Références

- `references/categories.md` — grille d'analyse section par section
- `references/report-template.html` — squelette HTML + design system complet
- `references/composants.md` — bibliothèque de blocs HTML à copier
- `references/scoring.md` — calcul des scores par domaine et du score global
- `references/collecte.md` — commandes de collecte par écosystème
- `scripts/collect-metrics.sh` — collecte automatisée des compteurs

