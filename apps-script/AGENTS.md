# AGENTS.md - E/apps-script (backend unifie)

## Role
Ce sous-dossier contient le backend Apps Script unifie (`Code.gs`) pour les apps AAR:
- `C - AAR PWA`
- `D - AAR READER HUB`
- `E - AAR READER HUB QWI`

## Contrats a maintenir
- Endpoints front: `status`, `listAars`, `getCatalog`, `setCatalog`, `upsert`, `delete`.
- `listAars` doit tolerer les JSON UTF-8 avec BOM (prefixe `\uFEFF`) pour ne pas masquer les AAR valides.
- Le catalogue persiste inclut `hashtags`, `factsHashtags`, `factsHashtagsConfigured`, `factsHashtagTooltipMap`, `countries`, `oaci`, `operations`, `exercises`, `oaciCountryMap`, `tooltipComments`.
- `factsHashtags` doit conserver l'ordre fourni par le hub QWI (pas de tri backend).
- `factsHashtagTooltipMap` doit accepter et persister les cles socle `BAAP_ROLE_*` ainsi que les cles dynamiques `FACTS_HASHTAG_*`.
- `tooltipComments` doit conserver les commentaires associes a ces cles dynamiques pour les boutons `#` personnalises.
- Automation ingest mail -> Drive: `runIngestEmailsToDrive`.
- Cle d'acces partagee: `AAR_ACCESS_KEY`.
- Dossier source JSON: `AAR_FOLDER_ID`.

## Regle AGENTS (obligatoire)
Si un agent modifie ce sous-dossier, il doit mettre a jour dans le meme changement:
1. ce `AGENTS.md`,
2. `../AGENTS.md` (hub QWI),
3. `../../C - AAR PWA/AGENTS.md`,
4. `../../D - AAR READER HUB/AGENTS.md`,
5. `../../AGENTS.md` (racine RETEX).

## Verifications minimales
- API health: `action=status`.
- Lecture: `action=listAars`.
- Ecriture QWI: `action=upsert` puis `action=delete`.
- Ingest: `action=runIngest` ou trigger horaire.
- Encodage texte requis: `UTF-8`.
- Recommande: JSON UTF-8 sans BOM a l'ecriture; la lecture backend reste compatible BOM.

## Communication obligatoire utilisateur
- Toute modification de `Code.gs` doit etre signalee explicitement a l'utilisateur dans la reponse finale.
- La reponse doit preciser si une action manuelle est requise cote Apps Script:
  - mise a jour du projet Apps Script distant,
  - redeploiement Web App (`/exec`),
  - mise a jour des Script Properties (`AAR_ACCESS_KEY`, `AAR_FOLDER_ID`, etc.),
  - re-autorisation si demandee par Google.
