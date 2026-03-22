# AGENTS.md - E/aar-pwa (copie deploiement formulaire)

## Role
Ce sous-dossier contient la copie de deploiement du formulaire AAR utilise par le HUB QWI (`./aar-pwa/AAR.html`).

## Schema formulaire synchronise (maj 2026-03-22)
- UI `AAR FLASH` renommee en `AAR BAAP` (valeur JSON conservee: `FLASH`).
- Les champs UI `LOG/TAC` et `Cadre TAC` sont masques (legacy uniquement).
- `0. Configuration`: `mission-config.js` embarque `hashtags` (catalogue general), `factsHashtags` (catalogue des boutons `1. FAITS`), `factsHashtagTooltipMap` (mapping `# -> infobulle`) et `tooltipComments`.
- `0. Configuration`: `factsHashtagTooltipMap` accepte les cles socle `BAAP_ROLE_*` et les cles dediees `FACTS_HASHTAG_*` (commentaire propre par bouton # personnalise).
- Les modules BAAP de l'onglet Faits sont obligatoirement synchronises:
  - `facts.baapSelected`,
  - `facts.baapAirfield`,
  - `facts.baapPilot`,
  - `facts.baapLoadmaster`,
  - `facts.baapMissionSupport`,
  - `facts.baapIntel`,
  - `facts.baapC2`.
- Les boutons `#...` de l'onglet Faits sont dynamiques (issus de `factsHashtags`) pour BAAP et WEAPONS SCHOOL, avec synchronisation bidirectionnelle vers `meta.hashtags`.
- Les boutons `#...` de l'onglet Faits respectent l'ordre de `factsHashtags` (ordre administrable dans le hub QWI).
- Les infobulles des boutons `#...` doivent suivre le mapping `factsHashtagTooltipMap` et les textes `tooltipComments` y compris pour les cles dynamiques.

## Couplage obligatoire
- Source de verite formulaire: `../../C - AAR PWA/AAR.html`.
- Le bridge d'edition QWI doit rester compatible avec `../qwi-mode.js`.
- Fichiers deploiement alignes: `AAR.html`, `mission-config.js`, `manifest.webmanifest`, `sw.js`, `icons/`.

## Validation avant propagation
- Toute mise a jour issue de NP est appliquee ici uniquement apres validation explicite utilisateur.
- Pas de synchronisation anticipee.

## Regle AGENTS (obligatoire)
Si un agent modifie ce sous-dossier, il doit mettre a jour dans le meme changement:
1. ce `AGENTS.md`,
2. `../AGENTS.md` (hub QWI),
3. `../../C - AAR PWA/AGENTS.md`,
4. `../../AGENTS.md` (racine RETEX).

## Verifications minimales
- Ouvrir un cycle complet `Nouveau` ou `Modifier` depuis le hub QWI.
- Verifier le retour `aar-qwi-save` vers le hub.
- Encodage texte requis: `UTF-8`.
