# Projet : app musicale David Baechler — V1

Tu vas développer une mini-application web musicale (PWA) pour David Baechler, artiste rock francophone. Lis ce document en entier avant de commencer. Les décisions qu'il contient sont déjà prises : ne les remets pas en question, mais signale-moi tout problème technique.

Le dossier contient aussi :
- `maquette/` : la maquette validée, écran par écran. **C'est la référence visuelle à reproduire fidèlement** (disposition, couleurs, tailles, typographie). Ces fichiers utilisent un format de maquette (balises `<x-dc>`, `<sc-for>`, `{{…}}`) : n'en reprends pas le code tel quel, reproduis la mise en page en HTML/CSS classique.
- `contenu/` : mes vrais fichiers (pochettes, MP3, photo, logo) et `infos.txt` avec tous les textes et liens.

---

## 1. Objectif

Une mini-app mobile-first, rapide et élégante, dédiée à ma musique. Pas un Linktree.

Parcours visé : une personne découvre ma musique sur TikTok → clique sur mon lien → écoute immédiatement → installe « l'app David » → la retrouve sur l'écran d'accueil de son téléphone.

## 2. Contraintes absolues

- Hébergement uniquement sur **GitHub Pages**, gratuit.
- Aucun CMS, aucune base de données, aucun backend, aucun abonnement.
- Ne propose pas Cloudflare, Firebase, Supabase, WordPress, Netlify, Vercel ni aucun service externe, à la seule exception de **Stripe** pour le paiement (section 9). Si une autre fonctionnalité exige un service, signale-le au lieu de l'ajouter.
- **Aucun compte utilisateur** : pas d'inscription, de login, d'e-mail ni de profil. On ouvre et on écoute.
- Technologie : **HTML, CSS et JavaScript simples**, sans framework, sans étape de compilation, sans npm. Je dois pouvoir modifier les fichiers directement.
- Priorité : simplicité, zéro abonnement, maintenance minimale.

## 3. Flux de travail et hébergement

Le dépôt GitHub contient tout le projet, publié par GitHub Pages. Quand je pousse une modification, le site se republie.

- Adresse prévue : **[davidrockmusic.github.io/david-app/ ou nom de domaine personnalisé]**. Tous les chemins (manifest, service worker, fichiers) doivent fonctionner avec cette adresse.
- Explique-moi comment tester en local avec un petit serveur (une PWA ne fonctionne pas en ouvrant le fichier HTML directement).
- Signale-moi si on approche des limites de GitHub Pages (environ 1 Go par site, 100 Mo par fichier, 100 Go de trafic par mois).

## 4. Périmètre de la V1

Trois onglets dans une barre fixe en bas : **Accueil**, **Musique**, **Bio**.

Écrans (voir `maquette/`) :
1. **Accueil** — `Main.dc.html`
2. **Musique / Discographie** — `Discographie.dc.html`
3. **Lecteur plein écran** — `Lecteur.dc.html`
4. **Bio et contact** — `Bio.dc.html`
5. **Aide à l'installation** (feuille qui monte du bas) — `Installer.dc.html`
6. **Débloquer les exclusifs** (feuille qui monte du bas) — `Debloquer.dc.html`

**Hors V1 :** l'espace Bonus (`Bonus.dc.html`, fourni pour information seulement) et tout onglet Live/Actus. Ne les construis pas, mais garde une architecture qui permettra d'ajouter des onglets plus tard sans tout refaire.

## 5. Architecture : application à page unique

- Une seule page HTML. Les sections s'affichent sans recharger la page (routage par `#`).
- **Indispensable** : le lecteur audio continue de jouer pendant qu'on navigue entre les sections.
- Le bouton retour du téléphone fonctionne normalement entre les sections.
- **Sur ordinateur** : pas de version séparée. L'app s'affiche dans une colonne centrée (largeur max ~480 px), sur un fond sombre, éventuellement avec ma photo en grand et floutée/assombrie derrière. Le bandeau d'installation est masqué sur ordinateur.

## 6. PWA

- `manifest.json`, icônes iOS et Android (à partir de mon logo, ou à défaut des initiales « DB » blanches sur fond #D2381F), couleur de thème #0F0E0D, affichage `standalone`.
- Un service worker qui met en cache l'interface (HTML, CSS, JS, images, polices) pour un chargement rapide.
- **Le service worker ne met jamais en cache les fichiers audio** (cause des bugs de lecture, surtout sur iPhone). Les MP3 viennent toujours du réseau.
- Un numéro de version du cache dans un seul endroit (le fichier de réglages), pour forcer la mise à jour quand je publie.

## 7. Installation

**Bandeau sur l'accueil** (voir maquette) : « **Garde ma musique sur ton téléphone**, comme une vraie app. Gratuit, sans compte. » + bouton « Installer ».

Comportement du bouton :
- **Navigateur intégré de TikTok, Instagram ou Facebook** : l'installation y est impossible. Détecte ce cas et affiche en priorité le message invitant à ouvrir le lien dans Safari ou Chrome (« … » → « Ouvrir dans le navigateur »).
- **iPhone dans Safari** : ouvre la feuille d'aide en 4 étapes de `Installer.dc.html`.
- **Android avec installation native disponible** : déclenche directement la boîte de dialogue d'installation.
- **Android sans installation directe** : instructions adaptées.
- **App déjà installée (mode standalone) ou ordinateur** : le bandeau disparaît.

## 8. Lecteur audio

- Liste des morceaux : pochette, titre, année, durée (lue automatiquement depuis le fichier, pas saisie à la main).
- **Mini-lecteur fixe** au-dessus de la barre d'onglets, visible dans toutes les sections (pochette, titre, Play/Pause, fine barre de progression rouge). Le toucher ouvre le lecteur plein écran.
- **Lecteur plein écran** : grande pochette, titre, barre de progression déplaçable, temps écoulé/total, précédent / Play-Pause / suivant, puis onglets Paroles / Crédits / Infos et liens Spotify et YouTube.
- API **Media Session** : titre, artiste et pochette sur l'écran verrouillé et dans les contrôles média du téléphone.
- Lecture en arrière-plan : à viser au mieux, en sachant qu'elle est limitée dans les PWA sur iPhone. Dis-moi clairement ce qui fonctionnera ou non.

## 9. Titres exclusifs et déblocage (prix libre)

Deux titres exclusifs apparaissent dans la discographie, verrouillés (voir maquette).

**Extraits**
- Chaque exclusif a un extrait public de 30 à 45 secondes avec fondu à la fin. **C'est un fichier séparé** : ne lis jamais seulement le début du titre complet.
- Si les extraits ne sont pas fournis, génère-les avec ffmpeg à partir du titre complet, au moment indiqué dans `infos.txt` (par ex. « à partir de 0:45 »).
- Toucher la pochette d'un exclusif verrouillé joue son extrait. Le bouton « Débloquer » ouvre la feuille de `Debloquer.dc.html`.

**Paiement**
- **Un seul paiement débloque tous les exclusifs** (présenté comme un soutien).
- Paiement via un **lien de paiement Stripe** en mode « le client choisit le montant ». Je crée ce lien moi-même dans Stripe : explique-moi précisément comment le régler. L'acheteur n'a besoin d'aucun compte (carte, Apple Pay, Google Pay).
- Dans la feuille Débloquer, les montants (3 €, 5 €, 10 €, Autre) sont indicatifs : le vrai montant se choisit sur la page Stripe. Adapte si besoin le texte pour que ce soit clair. En V1, remplace « et tout l'espace Bonus » par une formulation sur les exclusifs actuels et à venir.

**Déblocage automatique**
- Après paiement, Stripe renvoie l'acheteur vers une adresse de l'app contenant un code secret, par exemple `…/#/merci?k=CODE`.
- L'app compare ce code à celui du fichier de réglages. S'il correspond : elle enregistre le déblocage sur l'appareil (localStorage, avec try/catch), affiche un écran de remerciement, puis la Discographie où les exclusifs apparaissent **débloqués, sans cadenas, comme les autres titres**. Pas de page en double.
- L'écran de remerciement **affiche aussi le code** avec un message du type : « Garde-le : il te servira si tu changes de téléphone ou si tu installes l'app. » (Sur iPhone, Safari, le navigateur de TikTok et l'app installée ont des mémoires séparées.)
- Lien « **J'ai déjà un code** » dans la feuille Débloquer : un champ pour saisir le code manuellement.
- Un code commun pour tous. Quand je change le code, les appareils déjà débloqués le restent (enregistre « débloqué », pas le code).
- **Pour changer le code, deux endroits seulement** : le fichier de réglages, et l'adresse de retour dans mon lien Stripe. Le guide final doit l'expliquer pas à pas.

**Limite acceptée** : les fichiers exclusifs complets sont dans le dépôt public. Quelqu'un de motivé peut les trouver, et le code peut se partager. C'est un choix assumé pour la V1 (jusqu'à ~20 soutiens). Range-les simplement dans un dossier au nom peu évident et ne les liste pas dans une page visible.

## 10. Contact

Bouton « Écrire » dans la page Bio : simple lien e-mail (`mailto:`) vers l'adresse dédiée indiquée dans `infos.txt`. Aucun formulaire ni service.

## 11. Fichiers et données

Structure proposée :

```
/audio            MP3 des titres
/audio/extraits   extraits des exclusifs
/[dossier-peu-évident]  titres exclusifs complets
/covers           pochettes
/images           photos
/icons            icônes PWA
/lyrics           un fichier texte de paroles par titre
/css
/js
songs.json        catalogue
settings.json     réglages et textes
```

**songs.json** (JSON pur, réutilisable dans une future app native) — champs par titre : `id`, `title`, `year`, `cover`, `audio`, `description`, `lyrics` (chemin du fichier texte), `credits`, `spotify`, `youtube`, `visible`, `featured`, `exclusive` (true/false), `preview` (chemin de l'extrait pour les exclusifs).

**settings.json** — bio, liens réseaux (TikTok, Instagram, YouTube, Spotify, Facebook), e-mail de contact, phrase du bandeau d'installation, lien de paiement Stripe, code de déblocage, version du cache.

Le site génère automatiquement listes, fiches et liens à partir de ces deux fichiers.

**Ajouter une chanson = 4 étapes** : MP3 dans `/audio`, pochette dans `/covers`, paroles dans `/lyrics`, une entrée dans `songs.json`, puis publier.

Optimise les images (pochettes en ~1000 px max et version vignette). Si je fournis des WAV, convertis-les en MP3 adaptés au web (~192 kbps).

## 12. Identité visuelle (reproduire la maquette)

Univers rock / hard rock francophone, influences années 80-90, présentation moderne et sobre. Fond sombre chaud, une seule couleur forte, typographie condensée façon affiche de concert.

**Couleurs**
- Fond : `#0F0E0D`
- Surfaces : `#1B1917`, `#1F1C1A` (mini-lecteur, pastilles), `#252220`
- Emplacements image / pochette : `#2A2622`
- Lignes et bordures : `#2C2925`, `#3A3631`
- Texte principal : `#F1EBE0` ; texte secondaire : `#A39B8F` ; paroles et bio : `#D9D1C4`
- Accent (boutons Play, barre de progression, bouton principal) : `#D2381F`, texte blanc
- Accent sur fond sombre (labels « NOUVEAU SINGLE », titre en lecture) : `#E9604A`
- Encadrés d'alerte (bandeau d'installation, avertissement TikTok) : fond `#2A1B17`, bordure `#5A2A20`

**Typographie** (Google Fonts)
- Titres : **Big Shoulders Display** 700 et 900, en majuscules
- Texte : **Archivo** 400, 500 et 600
- Petits labels : 12 px, 600, majuscules, espacement 0,2em

**Composants**
- Bouton Play principal rond rouge (60 px sur l'accueil, 80 px dans le lecteur)
- Pastilles des réseaux : 40 px de haut, arrondies, fond `#1F1C1A` ; sur l'accueil elles défilent horizontalement. Utilise les icônes officielles des réseaux si possible, dans le respect de leurs règles de marque.
- Barre d'onglets : 68 px, icônes au trait, onglet actif en `#F1EBE0`, inactifs en `#A39B8F`
- Feuilles qui montent du bas : coins arrondis 20 px, fond `#1B1917`, poignée en haut
- Zones tactiles d'au moins 44 px ; contrastes accessibles ; vrais `<button>` et `<a>`, `aria-label` sur les boutons-icônes
- Pas d'emoji, pas de dégradés décoratifs

## 13. Méthode de travail

Ne génère pas tout d'un coup. Procède par étapes et attends ma validation entre chacune :

1. Lis la maquette et le contenu, puis propose l'arborescence des fichiers et un `songs.json` / `settings.json` remplis avec mes vraies données.
2. Construis l'interface et le lecteur (Accueil, Musique, Lecteur, Bio), testable en local.
3. Ajoute la PWA et l'installation.
4. Ajoute les exclusifs, les extraits et le déblocage, puis guide-moi pour créer le lien Stripe.
5. Mets en ligne sur GitHub Pages.

À chaque étape, choisis la solution la plus simple à maintenir moi-même.

À la fin, fournis un fichier `GUIDE.md` court, en français simple, expliquant où et comment modifier :
- une chanson (ajout, retrait, titre mis en avant) ;
- un texte (bio, bandeau) ;
- une photo ou une pochette ;
- un lien réseau ou l'e-mail ;
- le code de déblocage (avec l'étape Stripe) ;
- la version du cache, et quand la changer.

## 14. Évolution future (à garder en tête, pas à construire)

Espace Bonus (inédits, maquettes, making-of, et une galerie « pour les vrais fans » des **versions alternatives des pochettes** : les 3-4 visuels non retenus pour certains singles), onglet Scène/Actus, vraie protection des exclusifs (chiffrement ou codes individuels), apps natives iOS/Android. Les données (`songs.json`, fichiers) doivent rester séparées de l'interface pour être réutilisables.
