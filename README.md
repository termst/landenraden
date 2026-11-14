# Geoflatter

Online multiplayer aardrijkskundespel voor 2 tot 6 spelers, ook op de telefoon. Peer to peer (WebRTC via Trystero), geen server, geen account, geen kosten.

## Bestanden

| Bestand | Inhoud |
|---|---|
| `index.html` | Hele spel (HTML, CSS, JS) |
| `data/countries.geojson` | Landgrenzen (Natural Earth 50m, 234 landen, zonder Antarctica) |
| `data/countries.json` | Hintdata per land (continent, talen, valuta, buurlanden, staatsvorm, export, religie, enz.) |

## Online zetten via GitHub Pages

1. Maak een account op github.com (als je die nog niet hebt).
2. Klik rechtsboven op **+** en kies **New repository**. Naam bijvoorbeeld `landenraden`. Zet hem op **Public**. Klik **Create repository**.
3. Klik op **uploading an existing file**. Sleep `index.html`, `README.md` en de map `data` erin. Klik **Commit changes**.
4. Ga naar **Settings**, dan **Pages** (linkermenu).
5. Bij **Source**: kies **Deploy from a branch**. Branch: `main`, map: `/ (root)`. Klik **Save**.
6. Wacht 1 tot 2 minuten. Je link verschijnt bovenaan: `https://JOUWNAAM.github.io/landenraden/`.

## Spelen

* Host: vul naam in, klik **Maak kamer**, deel de link of de 4 letter code.
* Vrienden: open de link (join gaat automatisch) of typ de code en klik **Join**. De hele link in het codeveld plakken werkt ook.
* Verbinden duurt 1 tot 15 seconden (na 25 seconden volgt een foutmelding).
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
* `CUR_FIX` (valuta-correcties op landcode) en `HINT_DEFS` (alle hinttypes, rang en groep) en `ANCHORS` (ijklanden voor de hint "vanaf")

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
| Tekenen | Raders als Silhouet. Tekenaar: het gemiddelde van de punten van de raders |
| Grens trekken | (overlap - overlap van een cirkel) / ((1 - overlap van een cirkel) x 0,85), tussen 0 en 1. Geen hints en geen tijdbonus |
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
| 55 s | 8, naam | 317 | 73 |

Fout land na 5 s, plek in Brussel: Nederland 216, Frankrijk 208, Duitsland 186, Polen 42, Spanje 24, Japan 0 (oud: 100 voor elk Europees land).

## Hints (landen)

Van vaag naar concreet. De eerste is gratis, de laatste is altijd een sterke (hoofdstad, vlag, domein, vroegere naam of alle buurlanden). De middelste hints komen om de beurt uit elke groep die aan staat, zodat geografie niet elke plek vult. Per familie hoogstens één hint per ronde (bijvoorbeeld valuta, symbool of gedeelde munt).

| Groep | Hints (rang) |
|---|---|
| Geografie | continent (0), halfrond (1), klimaat (1), rijrichting (2), ligging aan zee (2), grootte (2), regio (2), afstand en richting vanaf een bekend land (3), oppervlakte tegenover Nederland (3) |
| Mensen | inwoners (2), talen (3) |
| Geld | gedeelde munt (3), symbool (3), naam van de valuta (4) |
| Codes | eerste cijfer landnummer (2), landnummer (5), domein (6, laatste) |
| Buren | aantal buurlanden (2), één buurland (4), alle buurlanden (6, laatste) |
| Naam en vlag | lengte hoofdstad (4), lengte naam (5), hoofdstad beginletter (6), vlag (6), naam (7, alleen bij 8 hints) |
| Geschiedenis en staat | staatsvorm (1), lid van EU, NAVO, ASEAN, Gemenebest of Afrikaanse Unie (3), jaar van onafhankelijkheid (3), los van welk land (4), hoort bij welk land (4, alleen gebieden), vroegere naam (6, laatste) |
| Economie | BBP per inwoner in 4 klassen (2), de twee grootste exportproducten (4), bekende merken (5) |
| Cultuur en sport | grootste religie (2), schrift (2), geliefde sport (4), gerecht (5), gastland Olympische Spelen (5), gastland WK voetbal (5) |

* Staat het continent vast (lobby of toernooironde), dan vervalt de continent-hint en begint de ronde met de regio.
* Een vage hint die binnen het werelddeel door meer dan 80% van de landen gedeeld wordt, valt weg (`INFO_KEYS`, `hintShare`). Halfrond of klimaat in Europa kost dus geen punten meer voor niets.
* Lengte en beginletter van de hoofdstad zijn één familie: samen in één ronde verklapten ze de hoofdstad bijna.
* Talen staan op volgorde van belang (hoofdtaal eerst), hoogstens drie.
* Bronnen van de nieuwe groepen: CIA World Factbook (staatsvorm, onafhankelijkheid, BBP per inwoner in koopkracht, export, religie). Lidmaatschappen, vroegere namen, merken, schrift, sport, gerechten en gastlanden zijn met de hand ingevuld. Sport en gerecht staan alleen bij landen waar ze iets zeggen (geen voetbal). Religie ontbreekt waar geen geloof duidelijk de grootste is (bijvoorbeeld China, Tsjechië, Zuid-Korea).
* Halfrond en klimaat gaan over het midden van het land, niet over de plek van de ronde. Grote landen geven dus elke ronde hetzelfde antwoord.
* "Vanaf" kiest het dichtstbijzijnde land uit `ANCHORS` dat geen buurland is en minstens 300 km weg ligt. Niet voor landen groter dan 2 miljoen km².

## Straatbeeld

Kies in de lobby de modus **Straatbeeld**. De foto's komen van [Panoramax](https://panoramax.xyz) (open data, geen key, geen kosten). De host zoekt de foto, controleert of hij laadt en stuurt hem naar iedereen.

* Dekking wisselt per land. De host zoekt in een willekeurig land en probeert een volgend land als er niets is. Lukt het niet, dan speelt die ronde op satelliet.
* 360-foto's gaan voor. Niet elk land heeft ze, dan krijg je een gewone foto waarin je beperkt kunt rondkijken.
* Moeilijk: je kunt niet rondkijken.
* De maker en licentie van de foto staan op het uitslagscherm.
* **Andere foto**: is de plek slecht (binnen, wazig, niets te zien), tik dan op **Andere foto**. Heeft iedereen gestemd, dan begint dezelfde ronde opnieuw op een andere plek: volle tijd, geen punten, eerdere antwoorden vervallen. Alleen spelen: één tik is genoeg. Werkt bij elke ronde met een straatfoto, dus ook in Nederland en Darts met foto.

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

## Tekenen

Kies in de lobby de modus **Tekenen**. Eén speler tekent de vlag van een land uit het hoofd, de rest ziet de vlag live ontstaan en raadt het land.

* **Om de beurt.** Elke ronde tekent de speler die het minst heeft getekend (bij gelijke stand wie het langst geleden tekende).
* **De tekenaar** ziet onderin de naam van het land en tekent op een vel van 3:2. Acht vaste kleuren (zwart, wit, rood, oranje, geel, groen, lichtblauw, blauw), drie kwasten, een rechthoek en een rondje, terug (ook Ctrl+Z) en wissen. Wit is de kleur van het vel en werkt als gum.
* **Vormen klikken vast** op de randen, de helft en een derde van het vel (`CONFIG.draw.snap`), zodat een driekleur strak wordt. De streepjes rond het vel wijzen die plekken aan.
* **De raders** krijgen hints zoals bij Satelliet, zonder de hint met de vlag.

| Moeilijkheid | Tekenaar |
|---|---|
| Makkelijk | Ziet de echte vlag als voorbeeld (tik om te vergroten) |
| Normaal | Alleen de naam |
| Moeilijk | Alleen de naam, alleen de kwast (geen vormen) |

**Punten.** Raders als Silhouet. De tekenaar krijgt het gemiddelde van de raders. Raadt minstens de helft het (`CONFIG.draw.half`), dan telt de ronde voor de tekenaar als goed.

**Netwerk.** Kwaststreken gaan elke 50 ms in stukjes naar iedereen, een vorm gaat bij loslaten als één stukje. Elk stukje heeft een volgnummer: mist een speler er een, dan vraagt hij de hele tekening op. Op het uitslagscherm staat de tekening naast de echte vlag.

* Met één speler wordt elke ronde Silhouet.
* Instellingen staan in `CONFIG.draw`.

## Grens trekken

Kies in de lobby de modus **Grens trekken**. Iedereen ziet dezelfde landnaam en tekent de omtrek op een leeg vel. Wie de vorm het best raakt, krijgt de meeste punten.

* **Tekenen:** elke lijn die je trekt sluit zichzelf tot een lus en wordt een vlak. Eilanden teken je als losse lussen. Een tik of een streepje telt niet. Terug (ook Ctrl+Z) en wissen staan naast **Klaar**.
* **Klaar** zet je tekening vast. Druk je niet, dan telt je laatste tekening bij het einde van de tijd. De host kent die steeds (zoals een pin bij Darts).
* **Plek en grootte maken niets uit.** De host maakt jouw tekening even groot als de echte vorm (zelfde oppervlakte), legt de zwaartepunten op elkaar en schuift nog een paar pixels voor de beste plek. Dan telt de overlap: doorsnede gedeeld door vereniging. De stand telt wel: noord is boven.
* **De nul is een rondje.** Een cirkel met dezelfde oppervlakte geeft 0 punten. Zo levert een rond land als Frankrijk niet vanzelf veel op, en een lang land als Chili niet vanzelf weinig. Volle punten krijg je bij 85% van de weg van rondje naar perfect (`CONFIG.trace.full`).
* **Geen hints en geen tijdbonus:** alleen de vorm telt. Vanaf 60% raak telt de ronde als goed, vanaf 25% als half (`good`, `half`).
* **Echte vorm:** zoals bij Silhouet, noord boven, lengte geschaald met cos(breedte). Overzeese delen vallen weg, eilanden tellen mee vanaf een deel van het hoofddeel.

| Moeilijkheid | Landen | Eilanden tellen vanaf | Extra |
|---|---|---|---|
| Makkelijk | Vanaf 250.000 km² | 8% van het hoofddeel | Kader in de verhouding van het land |
| Normaal | Vanaf 60.000 km² | 3% | Geen |
| Moeilijk | Vanaf 8.000 km² | 1% | Geen |

* Op het uitslagscherm zie je per speler de tekening over de echte vorm, even groot gemaakt zoals de host rekende. De beste krijgt een gouden rand.
* Continent, kleine landen en verdeling (gelijk of naar oppervlakte) werken zoals bij Satelliet. Aantal hints, hint elke en soorten hints zijn hier verborgen.
* **Sabotage:** Blackout laat het vel staan maar verbergt je eigen lijnen (blind tekenen). Jetlag werkt gewoon. Delusion wacht op de volgende ronde met hints.
* **Netwerk:** alleen de lussen gaan naar de host (gehele getallen op een vel van 1000 x 1000, hoogstens 30 lussen en 1200 punten). Elke speler rekent het plaatje op het uitslagscherm zelf na uit de eigen GeoJSON.
* Instellingen staan in `CONFIG.trace`.

## Sabotage

Zet in de lobby **Sabotage** aan (vanaf 2 spelers). Spelers krijgen kaarten en spelen die op elkaar.

| Kaart | Wat het doet | Wanneer |
|---|---|---|
| **Blackout** | Het doelwit ziet de kaart, foto, vorm of tekening niet meer. Hints werken nog. Straatjacht: alleen het satellietbeeld valt weg | Meteen, of de hele volgende ronde als je hem op het uitslagscherm speelt |
| **Delusion** | Alle hints van het doelwit gaan over een ander, willekeurig land uit hetzelfde werelddeel (zelfde soorten hints, andere antwoorden). Het doelwit hoort niets | Altijd de volgende ronde. Komt uit op het uitslagscherm |
| **Jetlag** | De klok van het doelwit loopt twee keer zo snel. Tijd op: invoer dicht | Meteen, of vanaf de start van de volgende ronde |

* **Varianten:** willekeurige speler (gewone rand), kies een speler (zilver), iedereen (goud).
* **Kaarten krijgen:** 1 bij de start, daarna elke 2 rondes, hoogstens 3 in je hand. Wie achter staat krijgt vaker een sterke variant (`CONFIG.sab.odds`).
* **Spelen:** hoogstens één kaart per ronde. Tik op een kaartje naast de chatknop.
* De tekenaar is niet te raken: een kaart die op hem wacht, wacht een ronde langer.
* `CONFIG.sab.mix: true` laat Delusion elke hint uit een ander land halen (losse onzin in plaats van één vals spoor).
* Alles staat in de gewone snapshot, net als de plek van de ronde. Wie in de browser zoekt kan het vinden.
* Het eindscherm toont de saboteur, het mikpunt en het aantal gespeelde kaarten.

## Toernooi

Kies in de lobby **Toernooi** (de gouden knop onderaan). Eén potje met meerdere modi, met eigen regels.

| Instelling | Opties | Wat het doet |
|---|---|---|
| Modi | Satelliet, Straatbeeld, Nederland, Straatjacht, Darts, Silhouet, Tekenen, Grens trekken (minstens één) | Welke modi meedoen. Tekenen valt weg als je alleen speelt |
| Volgorde | Door elkaar, Om de beurt, Per blok | Door elkaar: evenveel per modus, nooit twee keer dezelfde na elkaar als het kan. Om de beurt: vaste rij die herhaalt. Per blok: eerst alle rondes van de ene modus, dan de volgende |
| Rondes | 3 tot 30 (teller, vasthouden gaat sneller) | Eronder staat hoeveel rondes elke modus krijgt |
| Moeilijkheid | Makkelijk, Normaal, Moeilijk, Oplopend | Oplopend: eerste derde makkelijk, dan normaal, laatste derde moeilijk |
| Continenten | Europa, Amerika, Azië, Oceanië, Afrika | Voor Satelliet, Straatbeeld, Darts, Silhouet, Tekenen en Grens trekken. Per ronde één continent, naar het aantal landen (of oppervlakte), zodat de ringen bij Darts kloppen |
| Provincies | De 12 provincies | Voor Nederland en Straatjacht. Raken de plaatsen op, dan eerst andere plaatsen in die provincies, pas daarna een herhaling |
| Plaatsen, Straatjacht speelt in, Beeld | Als in de losse modi | Alleen zichtbaar als een modus die ze gebruikt meedoet |
| Finale telt dubbel | Aan of uit | Laatste ronde x2 punten |

* Tik bij continenten of provincies vanuit "alles aan" op één tegel: dan staat alleen die aan. Tik meer om toe te voegen, **Alle** zet alles terug.
* De host maakt het schema bij de start. Elke ronde begint met een kaartje (ronde, modus, uitleg) en de rondechip toont de modus.
* Op het uitslagscherm zie je het schema (gespeeld, nu, nog te gaan) en de volgende modus op de knop. Het eindscherm toont punten per modus, de beste per modus in goud.
* Geen foto of straatdata: die ronde wordt Satelliet of Nederland en telt ook zo.
* Statistieken tellen een toernooi als eigen modus. Instellingen staan in `CONFIG.tour`.

## Avatars en kleuren

Avatar en kleur kies je in twee uitklapmenu's in de lobby. De avatarlijst heeft 24 opties: letter, hengelaarsvis, vliegenzwam, dwaallicht, gekke bek, sterrenslak, appel, ufo, badeend, cactus, uil, spook, robot, schedel, boterham, deftige inktvis, huilende ui, kies, kabouter, klomp met tulp, kwal, cycloop, wolk op laarzen en xavi. Een opgeslagen avatar die niet meer bestaat valt terug op de letter (`avKey`). De kleurlijst heeft 24 kleuren en een kleurkiezer voor elke `#rrggbb`. Bij een lichte kleur wordt het icoon automatisch donker (`isLight` in `index.html`), zodat het altijd zichtbaar blijft.

Een avatar toevoegen: een `<symbol id="av-<id>">` (32 bij 32, wit op de spelerskleur, gecentreerd op 16,16) in de sprite bovenin, de id in `AVATARS` en de tekst `av_<id>` in `I18N`.

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
