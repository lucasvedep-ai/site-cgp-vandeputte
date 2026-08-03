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

**Palette (bleu nuit + or)** — décision finale actée :
```css
--bleu: #0B1F3A       /* bleu nuit / "bleu roi" */
--or: #C9A84C          /* or champagne */
--gris-clair: #F4F6FA
--texte-sec: #5a6278
```

**Polices Google** : Playfair Display (titres), Inter (corps), Cinzel (logo LV, eyebrows).

**Logo LV** : SVG inline (polygon L or massif + path V blanc tracé + ligne or), présent en nav, footer, et placeholder À propos. Décision finale actée : Logo LV variante "D1" + typo Cinzel.

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

## 7. Formulaire — pipeline prévu (pas encore branché)

```
Formulaire site → Google Form (mêmes champs) → Google Sheets
→ n8n (polling) → Vivier Prospects Google Sheets
```

- **Canal source** (8 options) : Recommandation d'un proche / QR code / Carte NFC / Site internet / Réseaux sociaux / Salon ou événement / Bouche à oreille sport / Autre
- **Opt-in téléphonique** obligatoire, conforme loi n°2025-594 (effective août 2026)
- Choix polling Sheets plutôt que webhook direct pour éviter l'exposition d'un localhost

## 8. Décisions actées (à ne pas remettre en question)

- **À propos en texte narratif** — les bullet points y feraient "cheap", la longueur crée la confiance
- **Scraping = outil de fond, pas stratégie principale** — bouche à oreille et prescripteurs restent le cœur de l'acquisition
- **Opt-in obligatoire** dans le formulaire (loi 2025-594)
- **Formulaire → n8n via Google Forms** (polling Sheets), pas de webhook direct
- **Logo LV D1 + typo Cinzel** — identité visuelle finale

## 9. Reste à faire

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
- [ ] Brancher le formulaire sur Google Form / pipeline n8n
- [ ] Remplacer les images base64 par des URLs hébergées (allègement du fichier)
- [ ] Affiner le responsive sur très petit mobile
- [ ] Compléter les mentions légales (RGPD, ORIAS, MIA)
- [ ] SEO : balises meta, title, description, Open Graph

## 10. Manière de travailler avec Lucas

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
