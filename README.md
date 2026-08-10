# Kruisjescup JO17

Kleine tool om de kruisjescup van het JO17-team bij te houden: na de eindpartij
van een training ken je direct een kruisje toe aan de winnaar(s). Aan het eind
van het seizoen zie je meteen wie er wint (schoenenbon t.w.v. €100).

Alle data (spelers, kruisjes, foto's) staat in een gratis Firebase-cloudproject
en wordt automatisch gesynchroniseerd — ook zonder bereik op het veld werkt
alles gewoon door, en zodra je weer verbinding hebt synct het vanzelf. Zo raak
je nooit meer iets kwijt, ook niet als je telefoon stuk gaat of kwijtraakt.

## Gebruik

Open `index.html` in de browser (op je telefoon werkt dit prima). Tabs onderin:

- **Home** — bovenaan de actuele stand van het seizoen, daaronder een
  weekkalender (maandag t/m zondag) die automatisch de huidige week toont —
  ook na een nieuwe maandag. Tik op een dag om de winnaar(s) van de eindpartij
  aan te vinken (meestal 1, bij een gelijkspel mag je er meerdere kiezen),
  eventueel met een notitie of foto. Met ‹ › blader je terug/vooruit om een
  gemiste week alsnog in te vullen.
- **Geschiedenis** — overzicht van alle toegekende kruisjes, te verwijderen
  bij een fout.
- **Spelers** — spelerslijst beheren.
- **Meer** — extra handmatige back-up (JSON) exporteren/importeren en alles
  wissen.

Een seizoen loopt van augustus t/m juli en wordt automatisch bepaald op basis
van de datum van de eindpartij; oudere seizoenen blijven bewaard en zijn te
bekijken via de seizoenkiezer bovenin. Bovenin het scherm zie je ook de
sync-status (☁️ gesynchroniseerd / 📴 offline).

## Cloud-opslag instellen (eenmalig, ~10 minuten)

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
         allow read, write: if request.auth != null;
       }
     }
   }
   ```
   **Publiceren**. (Dit betekent: alleen de app zelf — die automatisch anoniem
   inlogt — mag lezen/schrijven. Niet 100% waterdicht zoals een echt
   inlogsysteem, maar voorkomt dat willekeurige bots de data kunnen benaderen.
   Voor een hobbyproject als dit is dat een prima afweging.)
5. Linkermenu **Build → Authentication** → **Aan de slag** → tabblad
   **Sign-in method** → zet **Anoniem** aan.
6. Ga naar **Projectinstellingen** (tandwiel linksboven) → onderaan bij
   "Jouw apps" → klik het `</>` (web) icoon → geef een naam, bv.
   `kruisjescup-web` → **App registreren** (Firebase Hosting hoeft niet
   aangevinkt).
7. Je krijgt een codeblok met `firebaseConfig = { apiKey: ..., ... }`. Open
   `firebase-config.js` in dit project en vul die 6 waarden in op de plek van
   `"VUL_HIER_IN"`.

Klaar — open (of herlaad) de app, bovenin verschijnt "☁️ gesynchroniseerd".

De gratis laag van Firebase (Spark-plan) heeft ruim voldoende capaciteit voor
één team dat een paar keer per week een kruisje registreert.

## Hosten op internet (optioneel, voor "Add to Home Screen" op iPhone)

Omdat het losse bestanden zijn zonder build-stap, kun je dit bijvoorbeeld
direct via GitHub Pages hosten: zet dit repo aan in **Settings → Pages**
(branch `main`, map `/root`) en je hebt een linkje. Open dat linkje in Safari
op je iPhone → deel-icoon → **Zet op beginscherm** voor een eigen app-icoon
dat volledig scherm opent.

## Data & privacy

Spelersnamen, data en foto's staan in jouw eigen Firebase-project (niet bij
Anthropic/Claude of een derde partij). De Firebase-configuratiewaarden in
`firebase-config.js` zijn niet geheim — die identificeren alleen je project;
de beveiliging zit in de Firestore-regels hierboven. Maak via het
"Meer"-tabblad af en toe nog een handmatige JSON-export als extra
back-up, bijvoorbeeld voor het geval je ooit naar een nieuw Firebase-project
wilt overstappen.
