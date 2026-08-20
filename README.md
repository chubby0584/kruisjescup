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
| `jeugdscrheden.nl/o17-1.html` | `o17-1.html` | Teampagina voor spelers en ouders: stand + tijdlijn van trainingen en wedstrijden met foto's. Achter een login; aanmelden kan alleen via een uitnodigingslink van de trainer. Alleen om te bekijken. |
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
- **Leden** — uitnodigingslinks maken (1 dag of 1 week) en intrekken, plus
  het overzicht van wie er een account heeft, met de mogelijkheid om iemand
  te blokkeren.
- **Meer** — uitloggen, extra handmatige back-up (JSON) exporteren/importeren
  en alles wissen.

Een seizoen loopt van augustus t/m juli en wordt automatisch bepaald op basis
van de datum; oudere seizoenen blijven bewaard en zijn te bekijken via de
seizoenkiezer bovenin. Bovenin zie je ook de sync-status
(☁️ gesynchroniseerd / 📴 offline), en een link **"Bekijk teampagina"** die
`o17-1.html` opent zoals ouders en spelers 'm zien.

## Gebruik (teampagina, `o17-1.html`)

Spelers en ouders komen hier binnen via een uitnodigingslink uit het
tabblad **Leden**. Ze maken eenmalig een account aan (naam, e-mail,
wachtwoord) en blijven daarna ingelogd. De pagina toont de stand van het
gekozen seizoen als podium (met een knop voor de hele lijst) en daaronder
een tijdlijn van alle trainingen en wedstrijden, met winnaar(s), notitie en
foto.

Onder elke training/wedstrijd staat een rij met zes vaste emoji's
(👍 ❤️ 😂 😮 👏 🔥). Een lid kan er per bericht precies één plaatsen —
nogmaals tikken haalt 'm weer weg, een andere kiezen vervangt de vorige.
Er is bewust geen tekstveld: geen los rondslingerend commentaar om te
modereren, alleen een korte, veilige manier om te laten weten dat iemand het
gezien heeft. De trainer ziet en kan meedoen aan dezelfde reacties, verder
is er geen apart moderatiescherm voor.

Bovenin staat een tip om de pagina als app op het beginscherm te zetten, met
een stap-voor-stap-uitleg voor iPhone en Android. Zie ook
[Accounts voor ouders en spelers](#accounts-voor-ouders-en-spelers).

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
4. Ga naar het tabblad **Regels** binnen Firestore en plak de regels uit
   [Firestore-regels](#firestore-regels) hieronder → **Publiceren**.
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
bovenin verschijnt "☁️ gesynchroniseerd".

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

## Firestore-regels

Plak dit in Firestore → **Regels** → **Publiceren**. Zonder deze regels kan
iedereen die een account aanmaakt ook kruisjes wijzigen of wissen.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    function isCoach() {
      return request.auth != null
             && request.auth.uid == '6mi8nevdUzeKHyfkxjDFreoPkEk2';
    }

    function isActiveMember() {
      return request.auth != null
             && exists(/databases/$(database)/documents/members/$(request.auth.uid))
             && get(/databases/$(database)/documents/members/$(request.auth.uid)).data.blocked != true;
    }

    // Uitnodigingslinks. De code in de link is het geheim, dus 'get' mag —
    // je moet 'm al kennen. Alleen de trainer maakt of verwijdert ze.
    match /invites/{code} {
      allow get:   if true;
      allow list, create, update, delete: if isCoach();
    }

    // Lidprofiel: je maakt bij registratie je eigen profiel aan. Alleen de
    // trainer kan blokkeren, wijzigen of verwijderen — en alleen de trainer
    // kan de hele ledenlijst opvragen.
    match /members/{uid} {
      // Aanmelden kan alleen met een uitnodiging die nog niet verlopen is.
      allow create: if request.auth != null
                    && request.auth.uid == uid
                    && request.resource.data.blocked == false
                    && exists(/databases/$(database)/documents/invites/$(request.resource.data.invite))
                    && get(/databases/$(database)/documents/invites/$(request.resource.data.invite)).data.expiresAt > request.time;
      allow get:    if isCoach() || (request.auth != null && request.auth.uid == uid);
      allow list:   if isCoach();
      allow update, delete: if isCoach();
    }

    // Spelers en kruisjes: lezen mag elk ingelogd, niet-geblokkeerd lid.
    // Schrijven kan alleen de trainer.
    match /players/{id} {
      allow read:  if isCoach() || isActiveMember();
      allow write: if isCoach();
    }
    match /entries/{id} {
      allow read:  if isCoach() || isActiveMember();
      allow write: if isCoach();

      // Emoji-reacties: iedereen die mag lezen mag ook reageren, maar
      // alleen op de eigen reactie (uid = document-id) en alleen met een
      // emoji uit de vaste set — geen vrije tekst.
      match /reactions/{uid} {
        allow read: if isCoach() || isActiveMember();
        allow write: if request.auth != null
                     && request.auth.uid == uid
                     && (isCoach() || isActiveMember())
                     && request.resource.data.keys().hasOnly(['emoji'])
                     && request.resource.data.emoji in ['👍','❤️','😂','😮','👏','🔥'];
        allow delete: if request.auth != null && request.auth.uid == uid;
      }
    }
  }
}
```

De UID in `isCoach()` is het account van de trainer
(`coach@kruisjescup.app`). Maak je ooit een nieuw trainersaccount, pas dan
zowel deze regel als `COACH_UID` in `o17-1.html` aan.

## Accounts voor ouders en spelers

De teampagina zit achter een login. Aanmelden kan alleen via een
**uitnodigingslink met een houdbaarheidsdatum**.

In het beheerscherm, tabblad **Leden**, maak je met één tik een link voor
**1 dag** of **1 week**. Die ziet er zo uit:

```
https://jeugdscrheden.nl/o17-1.html?uitnodiging=<code>
```

Deel 'm in de teamgroep. Iedereen die 'm binnen die periode opent, kan een
account aanmaken met naam, e-mailadres en wachtwoord, en blijft daarna
ingelogd. Is de link verlopen, dan krijgt een nieuwe bezoeker netjes te zien
dat hij bij jou een nieuwe moet vragen — bestaande accounts blijven gewoon
werken. Je kunt een link ook eerder intrekken met **Verwijderen**.

De vervaldatum wordt afgedwongen in de Firestore-regels, niet in de website.
Aan de link sleutelen of hem later opnieuw proberen helpt dus niet.

**Wat dit wel en niet oplost.** Het houdt zoekmachines en willekeurige
voorbijgangers buiten de deur — foto's van de spelers zijn niet publiek
opvraagbaar — en een oude link die nog ergens in een groepsapp staat, is na
afloop waardeloos. Wat het niet tegenhoudt: iemand die de link binnen de
looptijd doorstuurt naar een buitenstaander. Daarvoor is het overzicht
hieronder.

Onder de uitnodigingslinks staat daarom het ledenoverzicht: van elk account
de naam, het e-mailadres en de aanmelddatum, nieuwste bovenaan. Bij elke link
zie je bovendien hoeveel mensen zich ermee hebben aangemeld — springt dat
aantal onverwacht omhoog, dan is de link verder verspreid dan bedoeld.
Klopt iets niet, dan blokkeer je het account met één tik — die persoon ziet
dan niets meer. Blokkeren is beter dan verwijderen: een verwijderd profiel
kan opnieuw worden aangemaakt, een geblokkeerd profiel niet. Iemand kan zich
uiteraard wel met een ander e-mailadres opnieuw aanmelden.

## Data & privacy

Spelersnamen, data en foto's staan in jouw eigen Firebase-project (niet bij
Anthropic/Claude of een derde partij). De Firebase-configuratiewaarden in
`firebase-config.js` zijn niet geheim — die identificeren alleen je project;
de beveiliging zit in de Firestore-regels hierboven en de wachtwoord-login.
Let op: de teampagina toont namen en trainingsfoto's van minderjarige
spelers. Die zijn nu niet meer publiek opvraagbaar — er is een account voor
nodig — maar iedereen die de link krijgt kan zo'n account aanmaken. Deel de
link dus alleen binnen het team, en controleer af en toe het tabblad
**Leden** in het beheerscherm. Maak via het
"Meer"-tabblad af en toe nog een handmatige JSON-export als extra back-up,
bijvoorbeeld voor het geval je ooit naar een nieuw Firebase-project wilt
overstappen.
