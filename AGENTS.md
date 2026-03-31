# AGENTS.md - E - AAR READER HUB QWI

## Role
Ce dossier (`E - AAR READER HUB QWI`) est le hub QWI (lecture + edition + suppression + publication).

## Points de schema a maintenir (maj 2026-03-24)
- Afficher `BAAP` pour le type AAR technique `FLASH` dans les filtres, cartes et details.
- Respecter l'anonymisation des identites:
  - detection prioritaire via `meta.identityAnonymized` / `meta.identityVisibility=QWI_ONLY`,
  - fallback compatibilite via valeurs neutres (`ANONYME` / `ANONYMISE`) si les flags sont absents,
  - fallback de marquage via `meta.hashtags` contenant `#ANONYME`,
  - contrainte UX: `#ANONYME` reste un marqueur technique interne et ne doit pas etre affiche dans les hashtags visibles (filtrage applique des l'extraction),
  - en vue QWI, conserver l'affichage du nom redacteur quand disponible,
  - ajouter un tag `ANONYME` en carte (style mission `LOG`) quand le dossier est anonymise,
  - rendu compact de secours: si les seules valeurs recues sont neutres, afficher une seule valeur `ANONYME` (pas de duplication).
- Inclure dans le rendu des faits les modules BAAP si presents:
  - `facts.baapSelected`,
  - `facts.baapAirfield`,
  - `facts.baapPilot`,
  - `facts.baapLoadmaster`,
  - `facts.baapMissionSupport`,
  - `facts.baapIntel`,
  - `facts.baapC2`.
- Le rendu texte (`facts/analysis/recos`) doit decoder les entites HTML (y compris doubles, ex: `&amp;#039;`) avant echappement final pour eviter les artefacts visuels.
- Maintenir la vue dediee `# / infobulles` (4e onglet) pour edition QWI de:
  - `catalog.factsHashtags` (boutons `#` visibles dans `1. FAITS`),
  - `catalog.factsHashtagTooltipMap` (mapping persistant `# -> infobulle`, conserve lors d'un renommage, avec cles socle `BAAP_ROLE_*` ou cles dediees `FACTS_HASHTAG_*`),
  - `catalog.tooltipComments` (commentaires d'infobulles).
  - Les autres hashtags de `0. CONFIGURATION` restent geres par l'administration catalogue classique.
- La vue `# / infobulles` est organisee en 2 sous-onglets:
  - `Boutons #` (gestion des boutons de `1. FAITS`),
  - `Commentaires` (edition des textes d'infobulles; l'ordre des commentaires des boutons `#` suit `factsHashtags` et se met a jour lors d'un renommage/ajout).
- Sous-onglet `Boutons #`: la liste est reordonnable en glisser-deposer et l'ordre est persiste dans `catalog.factsHashtags`.
- Glisser-deposer: afficher un indicateur visuel de depot (bande visible) exactement entre 2 hashtags (position d'insertion reelle).
- Sous-onglet `Boutons #`: pas de scroll interne dedie a la liste des hashtags (utiliser le scroll global de page).
- Le filtre `Hashtag` du HUB QWI est en multi-selection avec recherche texte:
  - logique d'application: ET (un AAR doit contenir tous les hashtags selectionnes),
  - persistance locale: tableau `hashtags[]` (avec compatibilite legacy de lecture `hashtag`),
  - UX de selection: panneau searchable avec compteurs d'occurrences (`n AAR`) + actions rapides (`Tout selectionner` / `Effacer` / `Inverser`) + suppression directe des hashtags deja selectionnes,
  - navigation clavier: dans la recherche hashtag, `fleche bas` / `fleche haut` pour naviguer, `Entree` pour selectionner/valider le hashtag actif,
  - contrainte d'affichage: le panneau des filtres hashtags doit rester au-dessus de la grille AAR (pas de recouvrement/coupure par les cartes),
  - `#ANONYME` reste masque dans les options visibles.
- Le filtre `Mission (LOG/TAC)` est retire du HUB QWI.
- Le filtre `Code OACI` le remplace:
  - selection unique (pas de multi-selection),
  - recherche texte + navigation clavier (`fleche bas` / `fleche haut` / `Entree`),
  - persistance locale via la cle filtre `oaci`.
- Le filtre `Pays` adopte la meme UX que `Code OACI`:
  - selection unique (pas de multi-selection),
  - recherche texte + navigation clavier (`fleche bas` / `fleche haut` / `Entree`),
  - persistance locale via la cle filtre `country`.
- Le filtre `Periode` encadre le volume affiche:
  - valeur par defaut: `6 derniers mois`,
  - option explicite: `Tout historique`,
  - persistance locale via la cle filtre `period`.
- Le filtre `DORESE` est independant du filtre `Classification`:
  - valeurs exposees: `DOCTRINE`, `ORGANISATION`, `RH`, `EQUIPEMENTS`, `SOUTIEN`, `ENTRAINEMENT`,
  - comportement: filtre sur la presence de la categorie DORESE choisie dans `recoCats`.
- Rendu visuel des tags en cartes:
  - hashtags `#...` en style gris neutre (`tag-hashtag`),
  - categories DORESE en rose (`tag-dorese`).
- Rendu tags DORESE (`DOCTRINE`, `ORGANISATION`, `RH`, `EQUIPEMENTS`, `SOUTIEN`, `ENTRAINEMENT`): utiliser une couleur dediee distincte des tags classification (`NP/DR/SSF`), anonymisation (`ANONYME`) et flotte (`AIRCRAFT`).

## Couplage obligatoire
- Base lecture: logique alignee avec `../D - AAR READER HUB`.
- Formulaire source: `../C - AAR PWA/AAR.html`.
- Le sous-dossier `aar-pwa/` est une copie de deploiement du formulaire source et doit rester synchronise.
- Protocole d'edition externe a maintenir:
  - Requete: `localStorage["aar_qwi_editor_request:<session>"]`
  - Ouverture: `aar-pwa/AAR.html?externalEditor=1&session=<session>`
  - Retour: `window.postMessage({ type: "aar-qwi-save", session, aar })`

## Validation avant propagation
- NP (`../C - AAR PWA`) est la reference fonctionnelle.
- Toute propagation vers ce hub QWI, sa copie `aar-pwa/`, et les equivalents DR se fait uniquement apres validation explicite utilisateur.

## Regle de livraison
Toute evolution de schema/champ/rendu AAR doit etre synchronisee avec:
1. `../C - AAR PWA`
2. `../D - AAR READER HUB`
3. `E - AAR READER HUB QWI/aar-pwa`
4. `AGENTS.md` impactes (racine + `C` + `D` + `E` + `E/aar-pwa` + `E/apps-script`)

## Backend Apps Script
- Source backend: `apps-script/Code.gs`
- Endpoints utilises: `listAars`, `upsert`, `delete`, `setCatalog`
- `listAars` doit rester compatible avec les JSON UTF-8 BOM (prefixe `\uFEFF`) pour eviter les AAR invisibles.
- Le timeout reseau Apps Script est parametre par `config.js -> appsScript.timeoutMs` (borne entre 5s et 120s, fallback 20s).
- En cas d'echec Apps Script, le hub tente automatiquement un fallback vers Google Drive, puis source statique si active.
- `setCatalog/getCatalog` doivent conserver `hashtags`, `factsHashtags`, `factsHashtagsConfigured`, `factsHashtagTooltipMap`, `oaciCountryMap` et `tooltipComments`.
- Au chargement du HUB QWI, le catalogue distant (`getCatalog`) est prioritaire sur le cache local navigateur pour garantir le meme ordre de boutons `1. FAITS` que la PWA.
- Automation ingest: `runIngestEmailsToDrive` (trigger)
- Query email de reference: `to:david.zemmour3@gmail.com newer_than:30d`
- Obligation de communication utilisateur: toute modification de `apps-script/Code.gs` (ou prerequis Apps Script associe) doit etre annoncee explicitement dans la reponse finale, avec les actions manuelles eventuelles a faire cote Apps Script (deploiement `/exec`, properties, autorisations).

## Regles d'encodage
- Encodage requis: `UTF-8`.
- En ecriture script: `-Encoding UTF8`.
- Controle anti-mojibake avant commit/push.

## Politique credentials
- `AAR_ACCESS_KEY` coherent entre:
  - `config.js` (ce dossier),
  - `../D - AAR READER HUB/config.js`,
  - `../C - AAR PWA/mission-config.js`.
