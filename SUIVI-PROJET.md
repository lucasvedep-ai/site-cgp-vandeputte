# Suivi du projet — Site web CGP Lucas Vandeputte

Ce document sert de **carnet de bord** du projet : contexte, état du site, décisions actées, reste à faire, et historique des versions. Il est mis à jour à chaque évolution significative du site pour garder une trace exploitable (backup + historique de modification).

---

## 1. Contexte

Lucas Vandeputte, Conseiller en Gestion de Patrimoine (CGP) en lancement, affilié **Cap Finances** (MIA), basé Lorient / Morbihan. Rémunération à la commission uniquement. Construit son infrastructure professionnelle de zéro (site, réseau, outils).

## 2. Fichiers du dépôt

| Fichier | Description |
|---|---|
| `index.html` | Site principal — fichier autonome (HTML + CSS + JS + images en base64 intégrées). Palette bleu nuit + or. |
| `docs/PASSATION_CLAUDE_CODE.pdf` | Note de passation reçue (contexte, décisions actées, reste à faire) — conservée comme référence historique. |
| `SUIVI-PROJET.md` | Ce document — suivi d'avancement et historique des versions. |

> Les variantes graphiques évoquées dans la note de passation (vert forêt, ardoise, noir, pétrole, premium, allégée) ainsi que les fichiers annexes (CRM Excel, carte de visite, stickers QR) n'ont pas été transmises dans cette session et ne sont donc pas dans le dépôt. À ajouter si Lucas souhaite les conserver aussi en historique.

## 3. Identité visuelle

**Palette actuelle (ardoise / laiton / mer)** — depuis la V2.0 :
```css
--slate:   #2E3639   /* ardoise — fonds sombres, texte principal */
--slate-d: #1F2528   /* ardoise foncée — hero, footer */
--foam:    #EDE9E1   /* écume — fond clair principal */
--foam-2:  #DFD9CE   /* écume soutenue — séparateurs, fonds alternés */
--brass:   #9C8355   /* laiton — accent sur fond clair */
--brass-l: #C4A97A   /* laiton clair — accent sur fond sombre */
--sea:     #3D5A63   /* bleu mer — sections actualité et coordonnées */
```

**Polices Google** : Fraunces (titres, serif à caractère) + Karla (corps, humaniste).

**Logo LV** : SVG inline conservé depuis la V1 (polygon L + path V + ligne), simplement recoloré — L en laiton, V en ardoise sur fond clair / écume sur fond sombre. Présent en nav et footer.

> **Historique** : la V1 utilisait bleu nuit `#0B1F3A` + or `#C9A84C` avec Playfair Display + Inter + Cinzel. Abandonné en V2.0 à la demande de Lucas : cette combinaison est devenue la signature visuelle par défaut des sites générés par IA, donc facilement identifiable comme telle — ce qui dessert un CGP qui vend de l'indépendance et du sur-mesure. Trois directions ont été maquettées (éditoriale sobre / ancrage breton / institutionnelle suisse), la seconde a été retenue.

## 4. Structure du site (ordre des sections)

1. **NAV** fixe — logo LV, liens ancres, téléphone 06 45 53 02 02 en or
2. **HERO** — split 50/50, accroche + 3 bullets + CTA, photo RDV (base64)
3. **ACTUALITÉ** — 3 cards (réforme retraites, éducation financière, inflation)
4. **SERVICES** — 6 cards (épargne, retraite, protection, défiscalisation, immobilier, transmission)
5. **POURQUOI UN CGP** — tableau comparatif Seul / Banque / CGP
6. **COMMENT ÇA MARCHE** — photo + 4 étapes
7. **À QUI JE M'ADRESSE** — 5 profils (salarié, artisan, jeune actif, famille, chef d'entreprise) + mention gratuité
8. **À PROPOS** — photo Lucas + texte narratif long (volontairement non condensé en bullets, voir décisions actées) + badges + stats Cap Finances
9. **TÉMOIGNAGES** — 3 cards (actuellement fictifs, à remplacer)
10. **COORDONNÉES** — 3 cards cliquables (tél, email, zone)
11. **FORMULAIRE CONTACT** — Prénom*, Nom*, Téléphone*, Email, Situation, Canal source* (8 options), opt-in téléphonique obligatoire, besoin libre
12. **FOOTER** — mentions légales MIA, logo LV, liens

## 5. Fonctionnalités JS en place

- Particules dorées animées dans le hero (canvas)
- Fade-up au scroll (IntersectionObserver) sur les cards
- Compteurs animés (30 min / 100% / 24h + stats Cap Finances)
- Validation du formulaire (champs requis + checkbox opt-in obligatoire)
- Message de confirmation post-envoi (pas encore branché à un backend)

## 6. Images embarquées (base64)

3 images intégrées directement dans le HTML (fichier autonome mais lourd, ~620 Ko) :
- Hero : photo simulation RDV
- Processus : photo bureau/banque
- À propos : photo Lucas détourée (fond transparent)

À terme : remplacer par des URLs hébergées pour alléger le fichier.

## 7. Formulaire — pipeline

```
Formulaire site → Google Form → Sheet "Formulaire_Site_Reponses_Brutes"
→ Make "Site -> Vivier Prospects" (3×/jour) → Vivier_Prospects (onglet Prospects)
```

**✅ CHAÎNE COMPLÈTE FONCTIONNELLE ET VÉRIFIÉE DE BOUT EN BOUT (05/08/2026)** — une soumission réelle depuis le site public traverse tout le pipeline et arrive dans le Vivier Prospects avec tous les champs correctement alignés.

**Côté site** : le formulaire crée dynamiquement un vrai formulaire HTML, soumis en POST vers un `<iframe>` invisible (`hidden_google_form_target`). Le visiteur ne voit jamais le Google Form, le design et l'UX du site sont inchangés.

### Pièges rencontrés et résolus (à ne pas re-subir)

1. **Google Form ni partagé ni publié** — deux réglages Google *distincts* : (a) le partage Drive doit être sur "Tous les utilisateurs disposant du lien", (b) le formulaire doit être **publié** (bouton dédié dans Google Forms, indépendant du partage Drive). Tant que l'un des deux manque, toute soumission est rejetée **silencieusement**.

2. **`fetch` en mode `no-cors` ne fonctionne pas de façon fiable** vers `formResponse` — Google le filtre différemment d'une soumission de formulaire classique. Remplacé par un vrai formulaire HTML + iframe caché. ⚠️ Effet pervers : le mode `no-cors` ne remonte **aucune erreur** au navigateur, donc le site affiche "succès" même quand Google rejette. Le message de confirmation ne prouve rien — toujours vérifier dans le Sheet.

3. **L'éditeur Make écrase la configuration** — rouvrir un onglet Make resté ouvert depuis un moment et sauvegarder restaure l'état mémorisé par cet onglet, écrasant les modifications faites entre-temps via l'API. C'est arrivé le 04/08 : le scénario a été silencieusement remplacé par une version de test (mauvaise feuille cible, 4 champs sur 12, planning désactivé). **Ne pas modifier le scénario depuis un vieil onglet.**

4. **Le déclencheur "Watch New Rows" suit par NUMÉRO DE LIGNE**, pas par contenu ni date. Vider la feuille source **désynchronise définitivement** le compteur (il attend la ligne 23 alors que la feuille en compte 2) et le scénario ne voit plus jamais rien, sans erreur apparente. Ce compteur ne se réinitialise pas via l'API : la seule solution fiable est de **recréer le scénario**.

- Google Form : `https://docs.google.com/forms/d/e/1FAIpQLSdVCRtCaE4jloVGGWT-sjlfEtJZNzvZBScijKqcw6GSVWp8Rg/`
- Sheet de réponses brutes : `Formulaire_Site_Reponses_Brutes` (dossier Drive `Prospects`)
- Mapping champ site → `entry.XXXXX` (obtenu via un lien prérempli de test, un identifiant par champ) :

| Champ | entry ID |
|---|---|
| Prénom | `entry.2035234336` |
| Nom | `entry.188678542` |
| Téléphone | `entry.1816082128` |
| Email | `entry.548477793` |
| Votre situation | `entry.1980637434` |
| Canal source | `entry.254791585` |
| Je viens de la part de qui ? | `entry.607530339` |
| Votre besoin | `entry.567632254` |
| Opt-in (case cochée envoyée comme `"Oui"`) | `entry.1741620044` |

### ⚠️ Ce qui casse (et ne casse pas) les automatisations

Les automatisations reposent sur **quatre points d'accroche** — et sur rien d'autre :

| Point d'accroche | Où |
|---|---|
| Les identifiants HTML des champs : `#prenom` `#nom` `#tel` `#email` `#situation` `#canal` `#recommande_par` `#besoin` `#optin` | `index.html` |
| L'URL du webhook Make | `index.html` (constante `MAKE_WEBHOOK`) |
| Les `entry.XXXXX` du Google Form | `index.html` (constante `ENTRY_IDS`) |
| L'iframe d'archivage `hidden_google_form_target` | `index.html` |

**Une refonte graphique ne casse rien** : la V2.0 a entièrement changé palette, polices et mise en page, les deux envois ont continué de fonctionner sans une seule modification côté Make ou Google Form (vérifié par interception des requêtes après refonte).

**Ce qui casse, en revanche** : renommer un champ, en ajouter un, ou en supprimer un. Toute modification du formulaire doit être répercutée **aux trois endroits** — le site, le Google Form (qui génère un nouveau `entry.XXXXX`), et le mapping du scénario Make. Ne jamais modifier le Google Form seul.

### Scénario Make "Site -> Vivier Prospects (instantané)" (id 6837179) — ACTIF

Déclenché **par webhook** à chaque soumission du formulaire : le prospect arrive dans le Vivier en quelques secondes.

- **Webhook** : `https://hook.eu1.make.com/6jfzyyk1vwtws5eu8yrqw8rqv1bw3ups` (structure de données `Formulaire site CGP - champs`, id 519266)
- **Mapping** : par nom de champ JSON (`{{1.prenom}}`, `{{1.nom}}`…) — plus simple que la version polling
- **Coût** : ~2 opérations par prospect, **zéro quand rien n'arrive** (contre ~90/mois de polling à vide)

### Scénario de secours "Site -> Vivier Prospects" (id 6837081) — DÉSACTIVÉ

Version par polling, conservée désactivée comme filet de sécurité : si le webhook tombe ou qu'une soumission est perdue, la réactiver permet de rattraper depuis l'archive `Formulaire_Site_Reponses_Brutes`. ⚠️ Ne pas l'activer en même temps que le webhook, sinon chaque prospect arrive **en double** dans le Vivier.

- **Fréquence** : toutes les 8 h (3×/jour) si réactivé
- **Mapping** : les champs Google Sheets arrivent **indexés par position** (`0` = Horodateur, `1` = Prénom, `2` = Nom, `3` = Téléphone, `4` = Email, `5` = Situation, `6` = Canal, `7` = Recommandé par, `8` = Besoin, `9` = Opt-in), pas par nom de colonne. Syntaxe Make à utiliser : `` {{1.`2`}} `` — les backticks sont **obligatoires**, sans eux `{{1.2}}` est interprété comme le nombre 1,2.
- **Écriture** : `useColumnHeaders: true` (mapping par nom d'en-tête, insensible à l'ordre des colonnes) + `tableFirstRow: "A1:AL1"`. ⚠️ Cette plage doit couvrir **toute** la largeur du tableau, colonne A comprise : une plage partielle décale toutes les valeurs d'une colonne.
- **Valeurs fixes** : `Source = "Site internet"`, `Statut pipeline = "À appeler"`, `Date d'ajout = maintenant`
- **Besoin** → écrit dans `Notes` avec le préfixe `Besoin initial (site) : ...`

**⏳ Reste à faire** : notification (SMS ou email) à chaque nouveau prospect — aucune connexion SMS n'existe dans le compte Make, et Twilio/Vonage facturent au message. Piste gratuite à explorer : notification par email via la connexion Google déjà en place.

**Note sur le format téléphone** : les numéros arrivent bruts (`0645530202`) et non formatés (`+33 6 45 53 02 02`). Une tentative de formatage automatique a échoué (fonctions de manipulation de texte Make difficiles à déboguer sans visibilité sur les valeurs intermédiaires) — mis de côté volontairement, sans impact fonctionnel.

- **Canal source** (8 options) : Recommandation d'un proche / QR code / Carte NFC / Site internet / Réseaux sociaux / Salon ou événement / Bouche à oreille sport / Autre
- **Je viens de la part de qui ?** (champ texte libre, optionnel) : n'apparaît que si "Recommandation d'un proche" est sélectionné dans le canal source (affichage conditionnel en JS, champ vidé si l'utilisateur change d'avis). Objectif : dans le Vivier Prospects, deux colonnes distinctes — le canal (ex. "Recommandation d'un proche") ET le nom du recommandeur — pour visualiser qui apporte le plus de prospects. Chaque champ de formulaire devient sa propre colonne dans Google Sheets indépendamment de sa visibilité conditionnelle sur le site : le fait qu'il soit caché par défaut ne change rien à la structure de données obtenue. À reporter comme colonne dédiée (ex. `Nom recommandeur`) dans le Vivier Prospects lors de la mise en place du pipeline n8n.
- **Opt-in téléphonique** obligatoire, conforme loi n°2025-594 (effective août 2026)
- Choix polling Sheets plutôt que webhook direct pour éviter l'exposition d'un localhost

## 8. Vivier Prospects — structure & intégration

Fichier existant : `Vivier_Prospects` (Google Sheets, dossier Drive `Prospects`), déjà en production. Il centralise tout le pipeline commercial de Lucas, alimenté par deux sources :

- **Scraping sortant** (Google Maps par zone géographique + métier ciblé, via le scénario Make **"AUTO vivier Prospect"**, bientôt migré vers n8n) → prospects B2B (artisans, professions libérales)
- **Formulaire du site** (à venir) → prospects B2C entrants, qualifiés eux-mêmes

**Structure actuelle de l'onglet `Prospects`** (colonnes A→AG, 33 colonnes) :
```
ID, Date d'ajout, Nom, Prénom, Entreprise, Niche, Secteur, Téléphone, Site web, Adresse, Ville,
Code postal, Min depuis Lorient, Min depuis Quimperlé, Min depuis Moëlan, Source, SIRET,
Statut pipeline, Date 1er appel, Résultat 1er appel, Date R1, Statut R1, Date R2, Statut R2,
Date R3, Statut R3, Date R4, Statut R4, Date signature, Priorité, Date de relance, Prochaine action, Notes
```

**Colonnes à ajouter (AH→AL)** pour accueillir les prospects du site, sans toucher aux colonnes existantes — ⏳ **action en attente côté Lucas** (ajout manuel des en-têtes, je n'ai pas d'accès en écriture aux Google Sheets) :

| Colonne | En-tête |
|---|---|
| AH | `Email` |
| AI | `Situation` |
| AJ | `Canal source` |
| AK | `Recommandé par` |
| AL | `Opt-in téléphonique` |

Le champ **"Besoin"** du formulaire (message initial du prospect) n'a **pas** sa propre colonne — il est fusionné dans la colonne `Notes` existante, préfixé pour rester identifiable : `Besoin initial (site) : <message>`. Ainsi le message d'origine du prospect et les notes de suivi que Lucas ajoute après appel/RDV cohabitent dans la même colonne, sans se confondre.

Les prospects du site auront `Source = "Site internet"` (au lieu de "Google Maps"), pour rester filtrables dans la même colonne `Source` déjà existante. Une fois qualifiés (1er appel et au-delà), ils suivent exactement le même tunnel que les prospects scrapés (`Statut pipeline` → `Date R1..R4` → `Date signature` → `Notes`), sans doublon de suivi.

**Vérifié le 2026-08-03** : le module Make `google-sheets:addRow` du scénario "AUTO vivier Prospect" a le paramètre `useColumnHeaders: true` (mapping par nom de colonne, pas par position). Ajouter ces 6 colonnes, dans n'importe quel ordre ou position, ne casse donc pas l'automatisation existante — elle continuera de viser uniquement les colonnes qu'elle connaît par leur nom exact.

## 9. Décisions actées (à ne pas remettre en question)

- **À propos en texte narratif** — les bullet points y feraient "cheap", la longueur crée la confiance
- **Scraping = outil de fond, pas stratégie principale** — bouche à oreille et prescripteurs restent le cœur de l'acquisition
- **Opt-in obligatoire** dans le formulaire (loi 2025-594)
- **Formulaire → n8n via Google Forms** (polling Sheets), pas de webhook direct
- **Logo LV D1 + typo Cinzel** — identité visuelle finale

## 10. Reste à faire

### Contenu (à fournir par Lucas)
- [ ] N° ORIAS personnel (badges, footer, carte de visite)
- [ ] Email définitif (supposé : l.vandeputte-mandataire@capfinances.fr — à confirmer)
- [ ] Photo Lucas fond pierre/neutre (remplace le placeholder LV dans À propos)
- [ ] Photo simulation RDV avec Lucas (remplace la stock photo du hero)
- [ ] 3 vrais témoignages clients (remplacent les fictifs)
- [ ] URL réelle du site (remplace `lucas-cgp.fr` dans QR codes + formulaire)
- [ ] Vérifier les chiffres Cap Finances (+150k clients, 22 Mds, 20 ans)
- [ ] Nom exact de la formation CGP + organisme RNCP

### Technique
- [ ] Lucas : ajouter les 5 colonnes `Email`/`Situation`/`Canal source`/`Recommandé par`/`Opt-in téléphonique` dans le Vivier Prospects (voir section 8)
- [ ] Brancher le formulaire sur Google Form / pipeline n8n
- [ ] Remplacer les images base64 par des URLs hébergées (allègement du fichier)
- [ ] Affiner le responsive sur très petit mobile
- [ ] Compléter les mentions légales (RGPD, ORIAS, MIA)
- [ ] SEO : balises meta, title, description, Open Graph

## 11. Manière de travailler avec Lucas

- Français, ton oral, direct
- Un pas à la fois — valider avant d'avancer
- Challenger plutôt que sur-expliquer
- Budget : gratuit ou quasi-gratuit uniquement
- Il corrige activement si quelque chose ne colle pas

---

## Historique des versions

| Version | Date | Résumé |
|---|---|---|
| **V1** | 2026-08-03 | Import initial du site de référence (`index.html`, palette bleu nuit + or, 1349 lignes) transmis via la note de passation. Mise en place du dépôt et du présent suivi de projet. Aucune modification de contenu à ce stade — reprise à l'identique de la V1 validée en amont. |
| **V1.1** | 2026-08-03 | Site hébergé publiquement sur GitHub Pages : `https://lucasvedep-ai.github.io/site-cgp-vandeputte/`. Correction responsive : le menu de navigation disparaissait entièrement sur mobile/tablette (<768px) sans moyen de le rouvrir — ajout d'un vrai menu hamburger (bouton + panneau déroulant avec les mêmes liens + téléphone). Testé sans débordement horizontal sur iPhone SE, iPhone standard, Samsung, tablette portrait/paysage, laptop et desktop. |
| **V1.2** | 2026-08-03 | Correction du tableau comparatif "Pourquoi un CGP" sur mobile : il fallait dézoomer ou scroller latéralement pour voir la colonne "CGP courtier". Le tableau se transforme désormais en cartes empilées sous 768px (un bloc par critère, les 3 réponses les unes sous les autres avec libellé), sans aucun scroll ni zoom nécessaire. Le tableau desktop/tablette est inchangé. |
| **V1.3** | 2026-08-03 | Ajout au formulaire de contact du champ optionnel "Je viens de la part de…" (texte libre), pour tracer qui a recommandé chaque prospect passé par le site. À reporter dans le Google Form (en cours de mise en place) et dans le Vivier Prospects comme colonne `Recommandé par`. |
| **V1.4** | 2026-08-03 | Le champ "Je viens de la part de qui ?" devient conditionnel : masqué par défaut, il n'apparaît (et ne se vide si on change d'avis) que lorsque "Recommandation d'un proche" est sélectionné dans le canal source — évite de le proposer aux visiteurs venus par un autre canal. |
| — | 2026-08-03 | (Pas de version du site) Documentation de la structure cible du Vivier Prospects : 6 colonnes à ajouter (`Email`, `Situation`, `Canal source`, `Recommandé par`, `Besoin`, `Opt-in téléphonique`), sans impact sur le scénario Make existant (mapping par nom de colonne confirmé). Action en attente côté Lucas. |
| — | 2026-08-03 | (Pas de version du site) `Besoin` retiré des nouvelles colonnes : fusionné dans la colonne `Notes` existante (préfixe `Besoin initial (site) : ...`) plutôt qu'une colonne dédiée. Seules 5 colonnes restent à ajouter (`Email`, `Situation`, `Canal source`, `Recommandé par`, `Opt-in téléphonique`). |
| **V1.5** | 2026-08-03 | Formulaire du site branché sur le Google Form créé par Lucas : envoi silencieux (`fetch` no-cors) vers l'endpoint `formResponse` à chaque soumission, mapping de chaque champ vérifié via lien prérempli de test. Design et UX du site inchangés, le visiteur ne voit jamais le Google Form. Reste à construire : le n8n qui transfère les réponses vers le Vivier Prospects. |
| **V1.6** | 2026-08-03 | Correction signalée sur iPad Safari : la case opt-in se cochait en interne mais ne l'affichait jamais visuellement (une règle CSS générique désactivait le rendu natif sans style de remplacement). Case redessinée en CSS, agrandie à 24px avec un vrai visuel coché, et toute la ligne rendue cliquable pour une meilleure cible tactile. |
| — | 2026-08-03 | (Pas de version du site) Pipeline formulaire → Google Form → Sheet validé en conditions réelles : deux soumissions test depuis le site confirmées dans `Formulaire_Site_Reponses_Brutes`, tous champs correctement mappés. Cause du blocage initial identifiée et documentée : Google Form ni partagé publiquement ni publié (deux réglages distincts). |
| **V1.7** | 2026-08-04 | Le `fetch` no-cors vers le Google Form ne passait plus de façon fiable (Google le filtre différemment d'une soumission de formulaire classique). Remplacé par un vrai formulaire HTML soumis vers un iframe caché, qui reproduit une soumission normale. |
| **V1.8** | 2026-08-05 | Correction du débordement de l'adresse email dans sa carte (section Coordonnées) : un bloc texte en flex ne peut pas rétrécir sous la largeur de son contenu sans `min-width: 0`. |
| **V1.9** | 2026-08-05 | Envoi instantané : le formulaire poste désormais vers un webhook Make (traitement immédiat) **en plus** du Google Form (conservé comme archive). Prospect visible dans le Vivier en quelques secondes au lieu de 8 h, et moins d'opérations Make consommées. |
| **V2.0** | 2026-08-05 | **Refonte graphique complète**, contenu strictement inchangé. Nouvelle identité ardoise / laiton / mer, Fraunces + Karla, hero en photo pleine largeur, logo LV conservé et recoloré. Motif : la palette bleu nuit + or et le duo Playfair/Inter sont devenus la signature visuelle des sites générés par IA. Corrections au passage : fragment HTML orphelin hérité de la V1 (légende « Sans stratégie » affichée en clair sous le tableau), styles inline pointant vers des variables supprimées, texte de l'opt-in passant en majuscules, puces de contact disparues, chevauchement nom/CTA sous 430px. Tableau comparatif : tient désormais entièrement dans l'écran sur desktop, cartes empilées sous 768px. |
