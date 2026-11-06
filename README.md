# Landenraden

Online multiplayer landenraadspel voor 2 tot 6 spelers. Peer to peer (WebRTC via Trystero), geen server, geen account, geen kosten.

## Bestanden

| Bestand | Inhoud |
|---|---|
| `index.html` | Hele spel (HTML, CSS, JS) |
| `data/countries.geojson` | Landgrenzen (Natural Earth 50m, 234 landen, zonder Antarctica) |
| `data/countries.json` | Hintdata per land (continent, talen, valuta, buurlanden, enz.) |

## Online zetten via GitHub Pages

1. Maak een account op github.com (als je die nog niet hebt).
2. Klik rechtsboven op **+** en kies **New repository**. Naam bijvoorbeeld `landenraden`. Zet hem op **Public**. Klik **Create repository**.
3. Klik op **uploading an existing file**. Sleep `index.html`, `README.md` en de map `data` erin. Klik **Commit changes**.
4. Ga naar **Settings**, dan **Pages** (linkermenu).
5. Bij **Source**: kies **Deploy from a branch**. Branch: `main`, map: `/ (root)`. Klik **Save**.
6. Wacht 1 tot 2 minuten. Je link verschijnt bovenaan: `https://JOUWNAAM.github.io/landenraden/`.

## Spelen

* Host: vul naam in, klik **Maak kamer**, deel de link of de 4 letter code.
* Vrienden: open de link (join gaat automatisch) of typ de code en klik **Join**.
* Verbinden duurt 1 tot 15 seconden.

## Testen

* Twee tabbladen: maak een kamer in tabblad 1, join in tabblad 2 met de code.
* Telefoon op 4G/5G (wifi uit) joinen bij een laptop op wifi. Lukt dat niet, stel TURN in (hieronder).

## TURN instellen (alleen als verbinden op mobiel faalt)

Sommige mobiele netwerken blokkeren directe verbindingen. Een gratis TURN server lost dat op.

1. Maak een gratis account op metered.ca (Open Relay, 20 GB per maand gratis).
2. Maak een app aan en kopieer je **username** en **credential**.
3. Open `index.html`, zoek `turn: [` in het CONFIG blok bovenaan het script.
4. Haal de `//` weg voor beide regels en vul je gegevens in.
5. Upload `index.html` opnieuw naar GitHub.

## Aanpassen

Alles staat in het `CONFIG` object bovenaan het script in `index.html`:

* `score`: punten voor goed, aftrek per hint, minimum
* `zoom` en `pan`: zoomniveau en bewegingsruimte per moeilijkheid
* `tiles`: kaartlaag. Standaard Esri satelliet (geen labels, geen key nodig). Wil je Mapbox: zet je URL met key in `tiles.main.url` en beperk de key in Mapbox op je GitHub Pages domein.
* `street`: zoekinstellingen voor straatbeeld
* `maxPlayers`, `resultAutoMs`
* `CUR_FIX` (valuta-correcties op landcode) en `HINT_DEFS` (alle hinttypes, rang en groep)

## Straatbeeld

Kies in de lobby de modus **Straatbeeld**. De foto's komen van [Panoramax](https://panoramax.xyz) (open data, geen key, geen kosten). De host zoekt de foto, controleert of hij laadt en stuurt hem naar iedereen.

* Dekking wisselt per land. De host zoekt in een willekeurig land en probeert een volgend land als er niets is. Lukt het niet, dan speelt die ronde op satelliet.
* 360-foto's gaan voor. Niet elk land heeft ze, dan krijg je een gewone foto waarin je beperkt kunt rondkijken.
* Moeilijk: je kunt niet rondkijken.
* De maker en licentie van de foto staan op het uitslagscherm.

Google Street View zit er niet in: dat vraagt een eigen API key met betaalaccount.

## Font

Beide fonts staan als base64 in `index.html`, dus ze laden meteen en zijn voor iedereen gelijk.

* **The Bold Font** (gratis versie, © Sven Pels): titels, knoppen en grote tekst. Alleen kapitalen en geen accenten, dus namen in dit font verliezen hun accent.
* **Madani** (demo, NamelaType): uitleg, hints, labels, invoer en chat. Teruggebracht tot Latijnse letters om de pagina klein te houden. De cijfers in de demo zijn doorgestreepte blokjes en leestekens als `- ' ( ) : / ? !` ontbreken: die komen uit The Bold Font. Met een gekochte versie kun je de cijfers en leestekens gewoon meenemen.

De demo van Madani is alleen voor persoonlijk gebruik. Koop een licentie op [namelatype.com](https://namelatype.com) voor promotioneel of commercieel gebruik, en controleer ook de licentie op [the-bold-font.com](https://the-bold-font.com/).

## Goed om te weten

* De host bepaalt alles (locatie, hints, timer, score). Valt de host weg, dan neemt de speler die het eerst joinde het over (binnen ongeveer 9 seconden).
* Iemand die wegvalt kan terugkomen via dezelfde link en houdt zijn score.
* De coördinaten van de ronde gaan naar elke speler (de kaart heeft ze nodig). Wie wil valsspelen kan ze in de browser opzoeken. Bij vrienden prima.
* Lokaal openen door dubbelklikken werkt niet (de browser blokkeert het laden van de data). Gebruik GitHub Pages, of lokaal `python -m http.server` in de map.

## Bronnen

Natural Earth (publiek domein), world-countries (ODbL), Esri World Imagery, Panoramax, Leaflet, Pannellum, Trystero, The Bold Font.
