# Kruisjescup JO17

Kleine tool om de kruisjescup van het JO17-team bij te houden: na de eindpartij
van een training ken je direct een kruisje toe aan de winnaar(s). Aan het eind
van het seizoen zie je meteen wie er wint (schoenenbon t.w.v. €100).

Alle data (spelers, kruisjes, foto's) staat in een gratis Firebase-cloudproject
en wordt automatisch gesynchroniseerd — ook zonder bereik op het veld werkt
alles gewoon door, en zodra je weer verbinding hebt synct het vanzelf. Zo raak
je nooit meer iets kwijt, ook niet als je telefoon stuk gaat of kwijtraakt.

Er zijn twee pagina's:

- **`index.html`** — het beheerscherm voor de trainer. Achter een
  toegangscode: hier ken je kruisjes toe, beheer je de spelerslijst en maak
  je back-ups.
- **`dashboard.html`** — een publieke, alleen-lezen pagina om te delen met
  spelers en ouders: de stand en een fotofeed van elke training. Geen login
  nodig, en niemand kan hier iets aanpassen.

## Gebruik (beheerscherm, `index.html`)

Open `index.html` in de browser en log in met de toegangscode. Tabs onderin:

- **Home** — bovenaan de actuele stand van het seizoen, daaronder een
  weekkalender (maandag t/m zondag) die automatisch de huidige week toont —
  ook na een nieuwe maandag. Tik op een dag om de winnaar(s) van de eindpartij
  aan te vinken (meestal 1, bij een gelijkspel mag je er meerdere kiezen),
  eventueel met een notitie of foto. Met ‹ › blader je terug/vooruit om een
  gemiste week alsnog in te vullen. Dinsdag/donderdag (de vaste trainingsdagen)
  kleuren oranje ("nog invullen") zolang die dag nog geen kruisje heeft.
- **Geschiedenis** — overzicht van alle toegekende kruisjes, te verwijderen
  bij een fout.
- **Spelers** — spelerslijst beheren.
- **Meer** — uitloggen, extra handmatige back-up (JSON) exporteren/importeren
  en alles wissen.

Een seizoen loopt van augustus t/m juli en wordt automatisch bepaald op basis
van de datum van de eindpartij; oudere seizoenen blijven bewaard en zijn te
bekijken via de seizoenkiezer bovenin. Bovenin het scherm zie je ook de
sync-status (☁️ gesynchroniseerd / 📴 offline).

## Gebruik (dashboard, `dashboard.html`)

Deel deze link met spelers/ouders (bijv. via de teamapp/WhatsApp). Toont de
stand van het gekozen seizoen (top 4, met een knop voor de hele lijst) en
daaronder alle trainingen op datum, met winnaar(s), notitie en — als die is
toegevoegd — de foto van die training. Alleen om te bekijken, geen
inlog nodig, niets is hier aan te passen.

## Cloud-opslag + login instellen (eenmalig, ~15 minuten)

De app heeft een gratis Firebase-project nodig om data in op te slaan. Dit
kan alleen jij doen (vereist inloggen met een Google-account) — hierna hoef
je er nooit meer naar om te kijken.

1. Ga naar [console.firebase.google.com](https://console.firebase.google.com)
   en log in met een Google-account (gratis).
2. **Project toevoegen** → geef een naam, bv. `kruisjescup` → Google Analytics
   mag je uitzetten (niet nodig) → **Project maken**.
3. Linkermenu **Build → Firestore Database** → **Database maken** → kies
   **Productiemodus** → kies een locatie dichtbij (bv. `eur3 (europe-west)`)
   → **Inschakelen**.
4. Ga naar het tabblad **Regels** binnen Firestore en plak:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read: if true;
         allow write: if request.auth != null
                       && request.auth.token.firebase.sign_in_provider == 'password';
       }
     }
   }
   ```
   **Publiceren**. Dit betekent: **iedereen** mag de data lezen (nodig voor
   het publieke dashboard, zonder dat daar een login voor nodig is), maar
   **wijzigen kan alleen** met een echte wachtwoord-login — dus alleen jij,
   via het beheerscherm met de toegangscode.
5. Linkermenu **Build → Authentication** → **Aan de slag** → tabblad
   **Sign-in method** → zet **E-mail/wachtwoord** aan (niet "Anoniem" — die
   mag je juist uit laten staan of weer uitzetten als hij nog aanstond).
6. Ga naar **Projectinstellingen** (tandwiel linksboven) → onderaan bij
   "Jouw apps" → als er nog geen web-app staat: klik het `</>` (web) icoon →
   geef een naam, bv. `kruisjescup-web` → **App registreren** (Firebase
   Hosting hoeft niet aangevinkt). Je krijgt een codeblok met
   `firebaseConfig = { apiKey: ..., ... }` — die waarden staan al in
   `firebase-config.js` in dit project (of vul ze aan als dat bestand nog
   leeg is).
7. Laat weten dat stap 5 klaar staat (Email/wachtwoord aangezet) — dan zet ik
   direct jouw gekozen toegangscode klaar als inlog voor het beheerscherm.

Zodra dat is gedaan: open `index.html`, log in met de toegangscode, en
bovenin verschijnt "☁️ gesynchroniseerd". Het dashboard (`dashboard.html`)
werkt daarna direct, zonder inloggen.

De gratis laag van Firebase (Spark-plan) heeft ruim voldoende capaciteit voor
één team dat een paar keer per week een kruisje registreert.

## Hosten op internet

Omdat het losse bestanden zijn zonder build-stap, kun je dit bijvoorbeeld
direct via GitHub Pages hosten: zet dit repo aan in **Settings → Pages**
(branch `main`, map `/root`) en je hebt een linkje. Open `index.html` in
Safari op je iPhone → deel-icoon → **Zet op beginscherm** voor een eigen
app-icoon dat volledig scherm opent. Het dashboard deel je gewoon als
kale link (`.../dashboard.html`) — dat hoeft niet op een beginscherm.

### Eigen domein (optioneel)

Wil je in plaats van `chubby0584.github.io/kruisjescup` een eigen domeinnaam
(bv. `kruisjescup.nl` of een subdomein van het clubdomein)?

1. **Registreer een domein** bij een registrar, bv.
   [TransIP](https://www.transip.nl) of [Namecheap](https://www.namecheap.com)
   — een `.nl`-domein kost meestal €5–10 per jaar.
2. Laat mij de gekozen domeinnaam weten — dan zet ik een `CNAME`-bestand in
   dit project klaar en geef ik je de exacte DNS-records die je bij je
   registrar moet instellen (meestal een paar A-records of een CNAME-record
   die naar `chubby0584.github.io` wijst).
3. In **Settings → Pages** van dit repo vul je daarna het domein in bij
   "Custom domain" en vink je **Enforce HTTPS** aan zodra dat beschikbaar is.

DNS-wijzigingen kunnen tot 24 uur duren voor ze overal werken.

## Data & privacy

Spelersnamen, data en foto's staan in jouw eigen Firebase-project (niet bij
Anthropic/Claude of een derde partij). De Firebase-configuratiewaarden in
`firebase-config.js` zijn niet geheim — die identificeren alleen je project;
de beveiliging zit in de Firestore-regels hierboven en de wachtwoord-login.
Let op: het publieke dashboard toont namen en trainingsfoto's van
minderjarige spelers aan iedereen met de link — deel die link daarom alleen
binnen het team (spelers/ouders), niet breder openbaar. Maak via het
"Meer"-tabblad af en toe nog een handmatige JSON-export als extra back-up,
bijvoorbeeld voor het geval je ooit naar een nieuw Firebase-project wilt
overstappen.
