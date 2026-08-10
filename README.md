# Kruisjescup JO17

Kleine tool om de kruisjescup van het JO17-team bij te houden: na de eindpartij
van een training ken je direct een kruisje toe aan de winnaar(s). Aan het eind
van het seizoen zie je meteen wie er wint (schoenenbon t.w.v. €100).

## Gebruik

Open `index.html` in de browser (op je telefoon werkt dit prima, ook offline
na de eerste keer laden). Geen installatie, geen server nodig — alle data
(spelers, kruisjes, foto's) wordt lokaal opgeslagen in de browser van dat
toestel.

Tabs onderin:

- **Toekennen** — kies de datum, tik de winnaar(s) aan (meestal 1, bij een
  gelijkspel mag je er meerdere aanvinken), voeg optioneel een notitie of foto
  toe, en sla op. Dit doe je direct na de training, dus geen foto's meer die
  je 2 maanden later moet uitzoeken.
- **Stand** — actuele ranglijst van het huidige seizoen.
- **Geschiedenis** — overzicht van alle toegekende kruisjes (met foto/notitie),
  te verwijderen bij een fout.
- **Spelers** — spelerslijst beheren.
- **Meer** — back-up exporteren/importeren (JSON, inclusief foto's) en alles
  wissen.

Een seizoen loopt van augustus t/m juli en wordt automatisch bepaald op basis
van de datum van de eindpartij; oudere seizoenen blijven bewaard en zijn te
bekijken via de seizoenkiezer bovenin.

## Hosten (optioneel)

Omdat het één los HTML-bestand is zonder build-stap, kun je het bijvoorbeeld
direct via GitHub Pages hosten: zet dit repo aan in **Settings → Pages** (branch
`main`, map `/root`) en je hebt een linkje dat je aan het hele team kunt geven.
Let op: elk toestel/browser heeft dan zijn eigen data — gebruik de back-up
export/import om data over te zetten of te delen.

## Data & privacy

Alles blijft lokaal in de browser (`localStorage`). Er wordt niets naar een
server gestuurd. Maak af en toe een export als back-up, want browserdata kan
verloren gaan (bijv. bij het wissen van browsergegevens of een nieuw toestel).
