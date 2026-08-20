# SC Rheden Jeugd — jeugdscrheden.nl

Teampagina's voor de jeugd van SC Rheden. Op dit moment draait er één team
(O17-1) met de kruisjescup: na de eindpartij van een training krijgt de
winnaar een kruisje, en aan het eind van het seizoen wint de koploper een
schoenenbon t.w.v. €100. Zaterdagen kunnen als wedstrijd worden vastgelegd
(tegenstander + uitslag, zonder kruisje).

Alle data (spelers, kruisjes, foto's) staat in een gratis Firebase-cloudproject
en wordt automatisch gesynchroniseerd — ook zonder bereik op het veld werkt
alles gewoon door, en zodra je weer verbinding hebt synct het vanzelf. Zo raak
je nooit meer iets kwijt, ook niet als je telefoon stuk gaat of kwijtraakt.

## Pagina's

| URL | Bestand | Wat |
|---|---|---|
| `jeugdscrheden.nl` | `index.html` | Portaal: clublogo met een knop per team. Hier komen later meer teams bij. |
| `jeugdscrheden.nl/o17-1.html` | `o17-1.html` | Publieke teampagina voor spelers en ouders: stand + tijdlijn van trainingen en wedstrijden met foto's. Geen login, niets aan te passen. |
| `jeugdscrheden.nl/beheer.html` | `beheer.html` | Beheerscherm voor de trainer, achter een toegangscode. |

### Een team toevoegen

1. Kopieer `o17-1.html` naar bijv. `o15-1.html`.
2. Zet in `index.html` een extra `<a class="team-btn">`-blok neer dat ernaar
   linkt.

Let op: alle teams delen op dit moment dezelfde Firestore-collecties
(`players` en `entries`). Een tweede team met een eigen stand vraagt dus
eerst om een aanpassing in het datamodel — bijvoorbeeld een `team`-veld op
elk document, of aparte collecties per team.

## Gebruik (beheerscherm, `beheer.html`)

Open `beheer.html` in de browser en log in met de toegangscode. Tabs onderin:

- **Home** — bovenaan de actuele stand van het seizoen, daaronder een
  weekkalender (maandag t/m zondag) die automatisch de huidige week toont —
  ook na een nieuwe maandag. Tik op een dag om de winnaar(s) van de eindpartij
  aan te vinken (meestal 1, bij een gelijkspel mag je er meerdere kiezen),
  eventueel met een notitie of foto. Met ‹ › blader je terug/vooruit om een
  gemiste week alsnog in te vullen. Dinsdag/donderdag (de vaste trainingsdagen)
  kleuren oranje ("nog invullen") zolang die dag nog geen kruisje heeft.
  Op zaterdag verschijnt een keuze Training/Wedstrijd; bij een wedstrijd vul
  je tegenstander en uitslag in en wordt er geen kruisje toegekend.
- **Geschiedenis** — overzicht van alles wat is vastgelegd, te verwijderen
  bij een fout.
- **Spelers** — spelerslijst beheren.
- **Meer** — uitloggen, extra handmatige back-up (JSON) exporteren/importeren
  en alles wissen.

Een seizoen loopt van augustus t/m juli en wordt automatisch bepaald op basis
van de datum; oudere seizoenen blijven bewaard en zijn te bekijken via de
seizoenkiezer bovenin. Bovenin zie je ook de sync-status
(☁️ gesynchroniseerd / 📴 offline).

## Gebruik (teampagina, `o17-1.html`)

Deel deze link met spelers/ouders (bijv. via de teamapp/WhatsApp). Toont de
stand van het gekozen seizoen als podium (met een knop voor de hele lijst) en
daaronder een tijdlijn van alle trainingen en wedstrijden, met winnaar(s),
notitie en foto. Alleen om te bekijken, geen inlog nodig.

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
7. De trainer logt in met een e-mail/wachtwoord-account uit stap 5. De
   ingestelde inlog staat in `beheer.html` als `COACH_EMAIL`, met de
   toegangscode als wachtwoord.

Zodra dat is gedaan: open `beheer.html`, log in met de toegangscode, en
bovenin verschijnt "☁️ gesynchroniseerd". De teampagina (`o17-1.html`)
werkt daarna direct, zonder inloggen.

De gratis laag van Firebase (Spark-plan) heeft ruim voldoende capaciteit voor
één team dat een paar keer per week een kruisje registreert.

## Hosten op internet

De site draait op GitHub Pages vanaf branch `main` (map `/root`), met
`jeugdscrheden.nl` als custom domain. Het bestand `CNAME` in de repo-root
legt dat domein vast — niet weghalen, anders valt het domein terug.

Benodigde DNS-records bij de registrar van `jeugdscrheden.nl`:

| Type | Naam | Waarde |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `chubby0584.github.io.` |

Daarna in **Settings → Pages** het domein invullen bij "Custom domain" en
**Enforce HTTPS** aanzetten zodra dat kan (kan tot een uur duren voordat het
certificaat klaar is). Controleer de IP-adressen bij twijfel in dat scherm of
in de GitHub Pages-documentatie — GitHub kan ze in principe wijzigen.

Voor op de telefoon: open `beheer.html` in Safari → deel-icoon → **Zet op
beginscherm** voor een eigen app-icoon dat volledig scherm opent.

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
