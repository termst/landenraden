# Geoflatter

Online multiplayer aardrijkskundespel voor 2 tot 6 spelers, ook op de telefoon. Peer to peer (WebRTC via Trystero), geen server, geen account, geen kosten.

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
* OP: de host zet in de lobby bij een speler de knop **OP** aan. Een OP kan de instellingen aanpassen en de lobby starten. Kicken, OP geven, volgende ronde en rematch blijven bij de host. OP blijft behouden als de host wisselt.

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

## Emotes

In een kamer staat naast de chatknop een knop met een lachende pin. Eén tik opent vijf stickers, een tweede tik verstuurt er één. Dezelfde vijf staan ook onder in het chatpaneel.

| Sticker | NL | EN | Bedoeld voor |
|---|---|---|---|
| Pin op de roos | Raak! | Bullseye! | Goede gok |
| Kompas met vraagteken | Waar?! | Where?! | Geen idee |
| Zandloper | Schiet op | Hurry up | Iemand is te traag |
| Pin in zee | Plons! | Splash! | Gok ver naast |
| Wereldbol met kroon | Kaartkoning | Map king | Respect |

* Anderen zien de sticker met jouw naam, avatar en kleur, ongeveer 3,5 seconden, met een eigen animatie en geluid (volgt de geluidsinstelling). Zelf zie je hem in de chat.
* Je kunt er één per 1,5 seconde sturen. Een ontvanger negeert meer dan één per 0,7 seconde per speler en toont maximaal 3 stickers tegelijk.
* Een emote toevoegen: één regel in `EMOTES` (SVG), één in `EMO_SFX` en de teksten `em_<id>` in `I18N`. De animaties staan in de CSS onder `.em-*`.

## Aanpassen

Alles staat in het `CONFIG` object bovenaan het script in `index.html`:

* `score`: maximum per ronde, tijdfactor, hintaftrek, naam-hint, fout maar dichtbij, vertragingscompensatie (zie Puntentelling)
* `zoom` en `pan`: zoomniveau en bewegingsruimte per moeilijkheid
* `tiles`: kaartlaag. Standaard Esri satelliet (geen labels, geen key nodig). Wil je Mapbox: zet je URL met key in `tiles.main.url` en beperk de key in Mapbox op je GitHub Pages domein.
* `street`: zoekinstellingen voor straatbeeld
* `maxPlayers`, `resultAutoMs`
* `CUR_FIX` (valuta-correcties op landcode) en `HINT_DEFS` (alle hinttypes, rang en groep)

## Puntentelling

Elke modus gebruikt dezelfde formule. De host rekent, aan het eind van de ronde.

```
punten = 1000 x nauwkeurigheid x hintfactor x tijdfactor
```

| Deel | Regel | Waarom |
|---|---|---|
| Nauwkeurigheid | 0 tot 1, per modus (zie onder), vloeiend | Kennis telt, geen sprongen op een grens |
| Hintfactor | Eerste hint gratis. Alle hints gezien: x0,5. Hint kost 1 + rang (sterker is duurder) | Zelfde aftrek bij 4, 6 of 8 hints |
| Naam-hint | Na de hint met de land- of plaatsnaam: x0,1 (telt wel als goed) | Het antwoord aflezen is geen aardrijkskunde |
| Tijdfactor | 70% plus 30% van je resterende tijd | Hints komen al met de tijd, dus tijd telt lichter dan eerst |
| Vertraging | De host neemt jouw tijd en aantal hints over, maximaal 1,5 s gunstiger dan wat hij zelf ziet | Wie niet host verliest geen punten aan het netwerk |
| Gelijke stand | Meeste goed, dan snelste goede antwoord | Geen willekeurige volgorde |

| Modus | Nauwkeurigheid |
|---|---|
| Satelliet, Straatbeeld | Goed 1. Fout: 0,25 x e^(-d/400 km), d = afstand van de plek tot de grens van jouw land |
| Nederland | Goed 1. Fout: 0,25 x e^(-d/10 km), d = afstand van de plek tot jouw plaats |
| Straatjacht | e^(-(d-20 m)/(0,17 x straal)) x (0,85 + 0,15 x deel ongebruikte scans) |
| Darts | e^(-(d-25 km)/1500 km), geschaald per continent. x2: dubbel binnen de binnenring, anders 0 |
| Silhouet | Als Satelliet. De plek is het punt in het hoofddeel dat het dichtst bij het midden ligt |
| Toernooi | Per ronde als de modus van die ronde. Finale aan: de laatste ronde x2 |

Fout telt met dezelfde hint- en tijdfactor, dus op hetzelfde moment wint goed altijd van fout. Het juiste continent of de juiste provincie geeft geen vaste punten meer: die staan al in de eerste hint.

Voorbeeld, standaard lobby (60 s, 6 hints, elke 10 s), goed land:

| Antwoord na | Hints | Oud | Nieuw |
|---|---|---|---|
| 5 s | 1 | 967 | 975 |
| 15 s | 2 | 810 | 883 |
| 25 s | 3 | 667 | 776 |
| 35 s | 4 | 537 | 656 |
| 45 s | 5 | 420 | 511 |
| 55 s | 6, hoofdstad | 317 | 363 |
| 55 s | 6, naam | 317 | 73 |

Fout land na 5 s, plek in Brussel: Nederland 216, Frankrijk 208, Duitsland 186, Polen 42, Spanje 24, Japan 0 (oud: 100 voor elk Europees land).

## Straatbeeld

Kies in de lobby de modus **Straatbeeld**. De foto's komen van [Panoramax](https://panoramax.xyz) (open data, geen key, geen kosten). De host zoekt de foto, controleert of hij laadt en stuurt hem naar iedereen.

* Dekking wisselt per land. De host zoekt in een willekeurig land en probeert een volgend land als er niets is. Lukt het niet, dan speelt die ronde op satelliet.
* 360-foto's gaan voor. Niet elk land heeft ze, dan krijg je een gewone foto waarin je beperkt kunt rondkijken.
* Moeilijk: je kunt niet rondkijken.
* De maker en licentie van de foto staan op het uitslagscherm.

Google Street View zit er niet in: dat vraagt een eigen API key met betaalaccount.

## Nederland-modus

Kies in de lobby de modus **Nederland**. Je krijgt een willekeurige plek in Nederland en raadt de plaats.

* Ongeveer 200 plaatsen (steden, dorpen en eilanden), met inwoners, provincie en coördinaten in `NL_RAW` in `index.html`. Een plaats toevoegen is één regel.
* De plek ligt binnen een paar km van de plaats, en de plaats is altijd de dichtstbijzijnde uit de lijst. Dat is het juiste antwoord.
* **Plaatsen**: alle, alleen steden (50.000+) of alleen dorpen. **Beeld**: satelliet of straatbeeld (Panoramax).
* Hints: provincie, landsdeel, inwoners, afstand en richting vanaf een grotere plaats, lengte van de naam, beginletter.
* Goed geeft de volle score. Fout telt naar afstand van jouw plaats tot de plek: maximaal 25%, elke 7 km verder halveert het (`CONFIG.score.nlNearKm`). Dezelfde provincie levert niets extra op, die staat al in de eerste hint.
* Op het uitslagscherm zie je de juiste plek en de gok van elke speler.

## Straatjacht

Kies in de lobby de modus **Straatjacht**. Je krijgt de naam van een straat of gebouw en een satellietkaart zonder namen, en je moet het doel vinden.

* **Tikken is scannen.** Elke tik zet je pin en laat zien wat daar ligt (straat of gebouw), met de omtrek op de kaart. Zo bouw je de kaart zelf op.
* **Warm of koud.** Na elke scan zie je hoe dicht je zit. Makkelijk: temperatuur, afstand en richting. Normaal: temperatuur en warmer/kouder. Moeilijk: alleen warmer of kouder.
* **Scans:** 8, 6 of 4 per ronde. Daarna kun je je pin nog wel verzetten, maar je krijgt geen informatie meer.
* **Raak?** Tik je op het doel (of ergens anders op dezelfde straat), dan ben je meteen klaar. Anders zet je je pin vast, of telt bij het einde van de tijd je laatste pin, zonder tijdbonus.
* **Score:** hoe dichterbij hoe meer, in verhouding tot de straal van het gebied (`falloffFrac`), zodat makkelijk en moeilijk even streng zijn. Ongebruikte scans tellen voor 15% (`scanShare`), naar verhouding en alleen zo zwaar als je dichtbij zit. Hints en tijd tellen zoals in elke modus.
* **Hints:** richting, afstand, buurt, postcode, een straat in de buurt en als laatste een cirkel op de kaart.
* **Waar speel je:** kies je eigen plaats (leuk met mensen die er wonen) of een willekeurige. Moeilijker betekent ook een groter gebied (800, 1200 of 1700 m straal).

De namen komen van [OpenStreetMap](https://www.openstreetmap.org/copyright) via [Nominatim](https://nominatim.org/release-docs/latest/api/Reverse/): geen key, geen kosten. Het spel doet per speler maximaal één verzoek per seconde, zoals hun [gebruiksregels](https://operations.osmfoundation.org/policies/nominatim/) vragen, en onthoudt antwoorden. Is Nominatim niet bereikbaar, dan speelt de ronde als gewone Nederland-ronde. Instellingen staan in `CONFIG.hunt`.

## Darts

Kies in de lobby de modus **Darts**. Je ziet een plek (satelliet of straatfoto) en gooit een pijltje op de wereldkaart waar jij denkt dat hij ligt.

* **Kaart** opent de wereldkaart, **Plek** brengt je terug. Tik op de kaart om je pijltje te zetten (verzetten mag), **Gooi** zet hem vast.
* **Punten** lopen vloeiend af met de afstand, zonder sprongen op de ringgrenzen: vol tot 25 km, daarna halveert het per 1040 km (`plateauKm`, `falloffKm`).
* **Ringen** rond de echte plek (wereld): roos 150 km, binnenring 400, middenring 800, buitenring 1600, rand 3200. Ze bepalen x2 en de kleur.
* **Per continent** schalen ringen en afval mee (`scale`): Europa 0,33 (roos 50 km), Afrika 0,6, Oceanië 0,55, Azië 0,75, Amerika 0,85. Anders zit in Europa iedereen in de roos en beslist alleen de snelheid.
* Hints en tijd tellen zoals in elke modus.
* **x2, dubbel of niks**: zet hem aan voor je gooit. Roos of binnenring telt dubbel, daarbuiten krijg je 0.
* Niet gegooid? Dan telt je pijltje bij het einde van de tijd, zonder tijdbonus.
* **Makkelijk** toont namen op de wereldkaart, normaal en moeilijk niet. Moeilijk zet de plek op slot, de wereldkaart kun je altijd bewegen.
* Continent, landkeuze, kleine landen en soorten hints werken zoals bij Satelliet. **Beeld** kiest satelliet of straatbeeld (Panoramax).
* Op het uitslagscherm zie je het dartbord rond de plek en ieders pijltje met afstand.
* Ringen en afval staan in `CONFIG.darts`. Afstand is over de bol (`gcKm`), niet plat.

## Silhouet

Kies in de lobby de modus **Silhouet**. Je ziet alleen de omtrek van een land op een leeg vel, en je raadt het land.

* Elke speler tekent de vorm zelf uit `data/countries.geojson`. De host stuurt alleen hoe hij getoond wordt (draaiing, spiegeling, welke delen), dus er gaat geen extra data over het netwerk.
* De vorm is op schaal: lengtegraden worden versmald met de cosinus van de breedte, zodat Rusland en Canada niet uitrekken. Landen over de datumgrens (Rusland, Fiji) blijven één geheel.
* Overzeese delen vallen weg: alleen delen binnen 1,5 keer de grootte van het hoofddeel (5 tot 25 graden) tellen mee. Frankrijk zonder Frans-Guyana, de VS zonder Alaska en Hawaï, Nederland zonder de Caribische eilanden.

| Moeilijkheid | Draaiing | Delen | Extra |
|---|---|---|---|
| Makkelijk | Noord boven | Alle delen vanaf 0,2% van het hoofddeel | Lengte en breedtelijnen zonder getallen, schaalbalk in km |
| Normaal | 15 tot 60 graden, links of rechts | Delen vanaf 1% | Geen |
| Moeilijk | Willekeurig, de helft van de keren gespiegeld | Alleen het grootste deel | Geen |

* Een label linksboven zegt of noord boven is, of dat de vorm gedraaid (en misschien gespiegeld) is.
* De vorm tekent zichzelf in ongeveer 1,5 seconde: eerst de omtrek, dan de vulling. Met "minder beweging" aan staat hij er meteen.
* Continent, landkeuze, kleine landen en soorten hints werken zoals bij Satelliet. Kleine landen staan standaard uit, want een stadstaat is een vlek.
* Op het uitslagscherm zie je het land op de kaart en naast de vlag de vorm zoals jij hem zag.
* Instellingen staan in `CONFIG.shape`.

## Toernooi

Kies in de lobby **Toernooi** (de gouden knop onder Darts en Silhouet). Eén potje met meerdere modi, met eigen regels.

| Instelling | Opties | Wat het doet |
|---|---|---|
| Modi | Satelliet, Straatbeeld, Nederland, Straatjacht, Darts, Silhouet (minstens één) | Welke modi meedoen |
| Volgorde | Door elkaar, Om de beurt, Per blok | Door elkaar: evenveel per modus, nooit twee keer dezelfde na elkaar als het kan. Om de beurt: vaste rij die herhaalt. Per blok: eerst alle rondes van de ene modus, dan de volgende |
| Rondes | 3 tot 30 (teller, vasthouden gaat sneller) | Eronder staat hoeveel rondes elke modus krijgt |
| Moeilijkheid | Makkelijk, Normaal, Moeilijk, Oplopend | Oplopend: eerste derde makkelijk, dan normaal, laatste derde moeilijk |
| Continenten | Europa, Amerika, Azië, Oceanië, Afrika | Voor Satelliet, Straatbeeld, Darts en Silhouet. Per ronde één continent, naar het aantal landen (of oppervlakte), zodat de ringen bij Darts kloppen |
| Provincies | De 12 provincies | Voor Nederland en Straatjacht. Raken de plaatsen op, dan eerst andere plaatsen in die provincies, pas daarna een herhaling |
| Plaatsen, Straatjacht speelt in, Beeld | Als in de losse modi | Alleen zichtbaar als een modus die ze gebruikt meedoet |
| Finale telt dubbel | Aan of uit | Laatste ronde x2 punten |

* Tik bij continenten of provincies vanuit "alles aan" op één tegel: dan staat alleen die aan. Tik meer om toe te voegen, **Alle** zet alles terug.
* De host maakt het schema bij de start. Elke ronde begint met een kaartje (ronde, modus, uitleg) en de rondechip toont de modus.
* Op het uitslagscherm zie je het schema (gespeeld, nu, nog te gaan) en de volgende modus op de knop. Het eindscherm toont punten per modus, de beste per modus in goud.
* Geen foto of straatdata: die ronde wordt Satelliet of Nederland en telt ook zo.
* Statistieken tellen een toernooi als eigen modus. Instellingen staan in `CONFIG.tour`.

## Avatars en kleuren

Avatar en kleur kies je in twee uitklapmenu's in de lobby. De avatarlijst heeft 25 opties (letter, 7 oorspronkelijke, de sterrenslak en 16 nieuwe: kat, vos, uil, beer, vis, spook, robot, schedel, raket, planeet, ster, xavi, kroon, diamant, vlam, pizza). De kleurlijst heeft 24 kleuren en een kleurkiezer voor elke `#rrggbb`. Bij een lichte kleur wordt het icoon automatisch donker (`isLight` in `index.html`), zodat het altijd zichtbaar blijft.

Een avatar toevoegen: een `<symbol id="av-<id>">` (32 bij 32, wit op de spelerskleur) in de sprite bovenin, de id in `AVATARS` en de tekst `av_<id>` in `I18N`.

## Opslag en statistieken

Alles staat in de browser (localStorage). Geen account, geen server. Cookies zijn hier niet geschikt: maximaal 4 KB per cookie en ze gaan bij elk verzoek mee naar de server.

| Sleutel | Inhoud |
|---|---|
| `lr_name`, `lr_color`, `lr_avatar` | Profiel |
| `lr_pid` | Speler-id om terug te komen in een kamer. Per apparaat, zit niet in de back-up |
| `lr_lang`, `lr_sound`, `lr_theme` | Voorkeuren |
| `lr_settings` | Laatste lobby-instellingen als host |
| `lr_stats` | Totalen, beste score per modus en de laatste 25 potjes (JSON, enkele KB) |

* **Statistieken**: knop linksboven op het startscherm. Potjes, gewonnen (alleen met anderen), goed, punten, gemiddelde per ronde, beste potje (gemiddeld per ronde), beste ronde, snelste goed, per modus en de laatste 10 potjes.
* Gemiddelde en records tellen per ronde: een potje van 15 rondes heeft altijd meer punten dan een van 5, dus het totaal zegt niets.
* Een potje telt zodra je het eindscherm ziet. Elk potje heeft een id, dus herladen of een hostwissel telt niet dubbel. Wie halverwege vertrekt, telt niet mee.
* **Back-up**: **Kopieer code** geeft een tekst die begint met `GF1`. Plak die op een ander apparaat en kies **Herstel**. Dat vervangt profiel, instellingen en statistieken op dat apparaat.
* Blokkeert de browser opslag (cookies uit, sommige privévensters), dan werkt het spel gewoon en meldt het statistiekenscherm dat er niets bewaard wordt.
* Safari wist opslag van een site die je 7 dagen (Safari-gebruik) niet opent. Op iPhone helpt **Zet op beginscherm**. Bewaar daarnaast een back-up code.

## Kaart zonder API key

Er is geen API key nodig. De wereldkaart bij Darts gebruikt CARTO-tegels. Laden die niet, dan schakelt de kaart na drie fouten over op Esri World Imagery. Wil je Mapbox of Google, pas dan `CONFIG.tiles` en `dartTiles()` aan.

## Font

De fonts staan als base64 in `index.html`, dus ze laden meteen en zijn voor iedereen gelijk.

* **The Logo Font** (demo, © The Bold Font, Sven Pels): titels, knoppen en grote tekst. De demo heeft alleen letters.
* **The Bold Font** (gratis versie, © Sven Pels): vult de cijfers en leestekens aan die in de demo van The Logo Font ontbreken.
* **Madani** (demo, NamelaType): uitleg, hints, labels, invoer en chat. Teruggebracht tot Latijnse letters om de pagina klein te houden. De cijfers in de demo zijn doorgestreepte blokjes en leestekens als `- ' ( ) : / ? !` ontbreken: die komen uit The Bold Font. Met een gekochte versie kun je de cijfers en leestekens gewoon meenemen.

**Let op, licentie.** De demo van The Logo Font mag volgens zijn licentie niet in een website worden ingebed of worden verspreid, ook niet gratis. Koop de volledige versie op [the-bold-font.com](https://www.the-bold-font.com/) voordat je dit online zet (die heeft ook cijfers). De demo van Madani is alleen voor persoonlijk gebruik: koop een licentie op [namelatype.com](https://namelatype.com) voor promotioneel of commercieel gebruik.

## Goed om te weten

* De host bepaalt alles (locatie, hints, timer, score). Valt de host weg, dan neemt de speler die het eerst joinde het over (binnen ongeveer 9 seconden).
* Iemand die wegvalt kan terugkomen via dezelfde link en houdt zijn score.
* De coördinaten van de ronde gaan naar elke speler (de kaart heeft ze nodig). Wie wil valsspelen kan ze in de browser opzoeken. Bij vrienden prima.
* Lokaal openen door dubbelklikken werkt niet (de browser blokkeert het laden van de data). Gebruik GitHub Pages, of lokaal `python -m http.server` in de map.

## Bronnen

Natural Earth (publiek domein), world-countries (ODbL), Esri World Imagery, OpenStreetMap en Nominatim (ODbL), CARTO, Panoramax, Leaflet, Pannellum, Trystero, The Logo Font, The Bold Font.
