# Ändringslogg

## 1.0.49

- Slår sparsamt upp telefonens avrundade position till ett ortnamn via
  OpenStreetMap och behåller resultatet i tilläggets lokala cache.
- Skickar ortnamnet med telefonens centrala väderprognos till Delin Hub-appen.

## 1.0.48

- Förnyar en utgången Glooko-session direkt med de sparade uppgifterna och
  gör om hämtningen utan att invänta nästa schemalagda körning.

## 1.0.47

- Hämtar väder från Open-Meteo var femtonde minut för Stugan och Nygatan.
- Tar emot en avrundad position från Tobias telefon och publicerar dess aktuella
  väder som en egen Home Assistant-sensor.
- Behåller senaste giltiga prognos vid tillfälliga källfel och märker den som
  gammal i stället för att tömma dashboarden.

## 1.0.46

- Märker varje börsvärde som `Pågående, fördröjd`, `Senaste stängning` eller
  `Senast sparad` och publicerar kurstiden i börsens lokala tid.
- Förtydligar på Börser-sidan att värdena inte ska tolkas som realtidskurser.

## 1.0.45

- Följer OMX Stockholm Benchmark GI och Cap GI som närmaste marknadsreferenser
  för Länsförsäkringar Sverige Index, samt OMXS30.
- Lägger till S&P 500, Nasdaq Composite, Dow Jones, Euro Stoxx 50, DAX,
  FTSE 100, Nikkei 225 och Hang Seng.
- Publicerar kurs, dagsändring i procent och punkter samt källans kurstid till
  Home Assistant var tionde minut. Senaste giltiga värde behålls vid tillfälliga
  källfel och markeras då som gammalt.

## 1.0.44

- Byter appnamnet i Home Assistant från Jämjö nätverksövervakning till Delin Hub.
- Lägger till samma hus- och pulsikon som telefonappen i Home Assistants appvy.

## 1.0.43

- Hindrar setresultat från avslutade volleybollmatcher utan starttid från att kopplas till föregående fotbollsmatch.
- Visar första eller andra halvlek för fotboll, handboll och bandy utifrån avsparkstiden när SVT Text saknar halvleksdata, utan att visa en påhittad matchminut.

## 1.0.42

- Ersätter de hårdkodade valen Kalmar FF och Kalmar HC med generella lagsökord.
- Följer som standard alla SVT Text-lag vars namn innehåller `Kalmar`, i samtliga sporter.
- Lägger till ett HA-fält där flera egna sökord kan anges med komma eller semikolon.
- Behåller Sverige som ett separat lagval med sportfilter och migrerar äldre Kalmar-inställningar automatiskt.

## 1.0.41

- Låter endast matcher som finns i SVT Texts målservice aktivera målservicen.
- CEV och HockeyAllsvenskan används enbart för att komplettera en SVT-match med tätare ställning, matchtid och händelser.

## 1.0.40

- Visar poängställningen i aktuellt set på volleybollens poänghändelser i stället för matchens setställning.

## 1.0.39

- Lägger till matchhändelser för samtliga sporter och publicerar dem som en del av den aktiva matchen.
- Använder HockeyAllsvenskans Game Center för periodtid, mål, utvisningar och timeout i Kalmar HC-matcher.
- Bygger ut testmatchen med händelsetyp, lag, spelare, matchtid och detaljtext.

## 1.0.38

- Visar halvlek och delresultat för fotboll, handboll och bandy när SVT Text levererar uppgifterna.
- Visar period och periodresultat för ishockey och innebandy samt set och poäng för volleyboll.
- Bygger ut testmatchen i Home Assistant med delnummer, delställning och knappar för nästa del.

## 1.0.37

- Roterar en tidigare inkonsekvent SSH-nyckel en gång vid migrering till GitHub-appen.

## 1.0.36

- Återskapar automatiskt ett trasigt eller inkonsekvent SSH-nyckelpar.

## 1.0.35

- Loggar SSH-klientens autentiseringsdiagnostik vid anslutningsfel.

## 1.0.34

- Synkroniserar SSH-publiknyckeln från den privata nyckeln vid varje appstart.

## 1.0.33

- Förbättrar SSH-diagnostiken vid anslutningsfel.

## 1.0.32

- Begränsar SSH till appens avsedda nyckel och publiknyckelautentisering.

## 1.0.31

- Loggar även den publika nyckel som faktiskt härleds från privatnyckeln.

## 1.0.30

- Loggar den publika OpenWrt-nyckeln vid varje appstart för enklare SSH-felsökning.

## 1.0.29

- Lägger till tydliga sensorer för senaste fondtransaktion, köp och försäljningar.
- Visar väntande transaktioner och de tio senaste händelserna som attribut i Home Assistant.

## 1.0.28

Avslutar svensk volleybollmålservice direkt när CEV markerar matchen som slut.
En gammal mellanställning som ligger kvar på SVT Text kan därmed inte hålla
matchen aktiv på telefonen och klockan.

## 1.0.27

Hämtar Glookos 14-dagars GMI i både procent och IFCC-enheten mmol/mol samt
genomsnittligt total-, basal- och bolusinsulin per dag. Värdena publiceras som
egna Home Assistant-sensorer och som attribut på Glooko-statusen för
telefonappen. GMI märks som ett uppskattat HbA1c och inte som ett laboratorieprov.

Svenska landskamper i volleyboll använder CEV:s officiella livekälla för
setställning och pågående setpoäng. SVT Text ligger kvar som reserv och för de
övriga sporterna.

## 1.0.26

Uppdaterar SVT Text var 20:e sekund under en aktiv målservicematch i stället
för en gång per minut. Det gör att poängen i pågående volleybollset når mobil
och klocka med betydligt mindre fördröjning. Intervallen för kommande matcher
och dagar utan bevakade matcher är oförändrade.

## 1.0.25

Låter Home Assistant-komponenten `jamjo_funds` ensam äga fondportföljen. Den
äldre fondmotorn i nätverkstillägget startas inte längre. Det gamla
`fund_portfolio_enabled`-fältet finns kvar enbart för kompatibilitet med sparade
inställningar. Därmed kan två separata transaktionsfiler inte längre publicera
mot samma sensorer och kommandon.

## 1.0.24

Lägger till en avstängd Glooko-insamlare för Omnipod 5. När den aktiveras i
tilläggets lokala inställningar publiceras synkstatus, senaste pumpdata och
dagens sammanräknade insulin, basal och bolus som MQTT-sensorer. Inloggningsfel
eller ändringar i Glookos webbgränssnitt kan inte stoppa nätverksövervakningen,
och kontouppgifterna publiceras aldrig till MQTT eller Git.

## 1.0.23

Publicerar kommande matcher från dagens SVT Text-målservice i
`upcoming_matches`. Listan använder samma lag- och sportfilter som den aktiva
målservicen, men innehåller bara matcher som fortfarande visas som `X-X`.

## 1.0.22

Anger separata MQTT-statusvärden (`ON` och `OFF`) för målservicens växlar.
Växlarna kan fortfarande skicka sina JSON-kommandon men visas nu med korrekt
läge i Home Assistant i stället för `unknown`.

## 1.0.21

Läser MQTT-adress och port direkt från Supervisor-miljön. Tidigare ändrade
startskriptet alla TOML-rader som hette `host` eller `port`; utan den gamla
inventeringsfilen kunde det även skriva MQTT-värdnamnet som adress på puckarna.
Puckarnas adresser lämnas nu alltid orörda.

## 1.0.20

Utökar SVT Text-målservicen till en gemensam livekanal för mobil och klocka.
Sverige kan följas per sport, Kalmar FF följs alltid som fotboll och Kalmar HC
som ishockey. Home Assistant får reglage för lag, sport och klockprioritet samt
ett 30-minuters testläge med valbart lag, sport, hemma/borta och resultat.
Testläget påverkar inte de äldre Kalmar-entiteterna och kan därför inte utlösa
befintliga riktiga målnotiser.

Versionen levereras som en förbyggd ARM64/AMD64-image från GHCR. Home Assistant
behöver därför inte längre bygga tillägget lokalt. Den gamla gemensamma
bootstrap-nyckeln är borttagen ur byggkontexten; varje installation använder
endast sin unika SSH-nyckel i den beständiga `/data`-volymen.

## 1.0.19

Rapporterar alla fondbelopp med ISO-valutan `SEK` i stället för visningsnamnet
`kr`. Det matchar Home Assistants statistikmetadata och tar bort lagningarna om
ändrade enheter utan att ändra beloppen eller historiken.

## 1.0.18

Tar bort den gamla Cloudflare Quick Tunnel-URL-vakten. Home Assistant använder nu
fast adress via `ha.jamjo.uk`, så addonen läser inte längre Puck 1-loggen och
skickar inte SMS, push eller e-post när den tidigare slumpade URL:en ändrades.

## 1.0.15

URL-byte skickas nu även till SMTP-målet `notify.jamjo_tobias`.

## 1.0.14

Lägger till övervakning av Cloudflare Quick Tunnel-adressen på Puck 1. När
adressen byts skickas en pushnotis till mobilen och ett SMS via ZTE-bryggan.
Den första adressen registreras utan att skicka larm.

## 1.0.13

Radion tystnade. Inte alltid, men ofta: tryck på P1, ett ögonblicks ljud, och
sedan sju till tjugotvå sekunders tystnad innan den kom igång. En hel eftermiddags
mätande gav den här listan över vad det **inte** var:

| Prövat | Resultat |
|---|---|
| Högtalarens wifi | lokal fil spelar på 0,28 s, felfritt i 30 s |
| Förbindelsen ut | 0 % paketförlust över 40 pingar |
| DNS och AdGuard | 0,05 s kallt, 0,00 s cachat |
| Chromecastens uppstart | 2,4 s efter tjugo minuters tystnad |
| IPv6 utan väg ut | avstängt på alla fyra puckar, ingen skillnad |
| Mesh-nätet | högtalaren sitter direkt på puck 1 |
| Home Assistant | tjänsten svarar på 0,5 s |

Det som återstod: **Sveriges Radios ström, och bara den.** Samma högtalare
spelar SomaFM utan ett enda avbrott i 26 sekunder. Två olika högtalare av två
generationer stannar båda på SR. Ingen av dem stannar på något annat.

Varken bandbredd eller förskott förklarar det – båda servrarna levererar
ungefär två och en halv gånger realtid (SR 230 kB på 8 s, SomaFM 372 kB).
Vad SR:s server gör som mottagarna inte tycker om vet jag fortfarande inte, och
jag kan inte ta reda på det: de blockerar CORS, så ingen webbläsare i huset får
läsa deras svar.

- **Ny modul `radiorelay.py`.** Tillägget håller strömmen mot Sveriges Radio,
  sparar de senaste åtta sekunderna i minnet, och serverar den vidare på
  `http://192.168.1.249:8099/radio/p1`. Högtalaren får hela försprånget på en
  gång i lokalnätets hastighet – samma väg som den lokala testtonen, som spelar
  på tre tiondelar.
- Kanalerna är `p1`, `p2`, `p3` och `p4kalmar`, med adresser hämtade ur
  Sveriges Radios eget API. Inga gissade adresser: `http-live.sr.se`, som jag
  först misstänkte, existerar inte.
- **Strömmen stängs efter tre minuters tystnad.** Förbindelsen är mobil och
  troligen betald per gigabyte; att hålla den öppen dygnet runt vore att lösa
  en irritation med en månadskostnad.
- En lyssnare som halkar mer än 512 kB efter kopplas bort i stället för att få
  äta minne. Pi:n har 900 MB och har hängt sig fyra gånger på ett dygn.
- Nya inställningar `radio_relay_enabled` och `radio_relay_port`, och porten
  8099 öppnas i tilläggets `ports`.
- 9 tester. Attrappströmmen levererar med paus, inte oändligt snabbt – utan det
  slog efterslämningsskyddet till och testet mätte fel sak.

## 1.0.12

Bakgrunden: Tobias stod i köket, telefonen satt på puck 1 med **-34 dBm**, och
`binary_sensor.tobias_narvaro` låg ändå kvar på `off`. Google Nest-högtalaren
sa därför ingenting när han kom fram. Puck 1:s DHCP-lista gav svaret:

```
e2:1f:fe:dc:31:e2   192.168.1.134   Tobias-mobil
```

Telefonen använder en slumpad MAC-adress (`e2` har biten för lokalt
administrerad adress satt), inte den `dc:69:e2:49:8c:ed` som stod i
inställningarna. En hårdkodad adress kan alltså sluta stämma utan att något
går sönder och utan att något syns i loggen.

- **`tobias_phone_mac` tar nu flera adresser**, separerade med komma, semikolon
  eller mellanslag. Android slumpar en adress per SSID, och nätet har tre.
- **Ny inställning `tobias_phone_hostnames`.** Tillägget läser
  `/tmp/dhcp.leases` från puckarna och matchar på namnet routern känner
  apparaten som – `Tobias-mobil`. Namnet överlever att adressen byts, vilket
  adressen per definition inte gör. Det här är den egentliga rättningen; flera
  MAC-adresser är bara bältet till hängslena.
- Namnlistan **delas mellan puckarna**. Bara den puck som är DHCP-server har
  filen, men den täcker hela nätet, så en klient på puck 3 får ändå sitt namn.
- **Ny sensor `sensor.natverket_anslutna_apparater`** med hela nätets
  klientlista: MAC, namn, puck, nätverk, signalstyrka, och en flagga för om
  adressen ser slumpad ut. Utan den gick det inte att skilja "apparaten är
  borta" från "apparaten har bytt adress" – det var precis den frågan som tog
  en halv förmiddag att svara på.
- `hostname` ligger **sist** i `ClientSample` med flit. Två ställen i `cli.py`
  bygger den med positionsargument, så ett fält i mitten hade tyst flyttat
  `network` till fel plats.
- 25 nya tester, varav två kör på de riktiga DHCP-raderna från puck 1 –
  inklusive raden där apparaten inte uppger något namn (`*`) och därför ska
  hoppas över, inte heta stjärna.

### Närvaro per person

Närvaron var hårdkodad till en person. Nu är den en lista.

- **Ny inställning `personer`** – namn, MAC-adresser och nätverksnamn per
  person. Varje person får tre entiteter: `binary_sensor.<namn>_narvaro`,
  `sensor.<namn>_signalstyrka` och `sensor.<namn>_ansluten_puck`.
- Rader utan namn, eller utan något att känna igen personen på, hoppas över.
  En person som matchar ingenting hade bara blivit en sensor som alltid står
  på av, och det är värre än ingen sensor alls.
- De gamla `tobias_*`-entiteterna finns kvar oförändrade. Dashboards som pekar
  på dem fortsätter fungera.
- 9 tester till.

## 1.0.11

- Rättar adressen i Coop-provet. `api-version=v2` är obligatoriskt – utan den
  svarar servern 404, vilket är exakt vad provet rapporterade. Provet visade
  därmed att Pi:n **når** Coops API; det var bara adressen som var ofullständig.
  Nästa start loggar vad API:et faktiskt svarar, och då går Coop att koppla in på
  riktigt.

## 1.0.10

- **Matsedeln syns nu som sju brickor, en per veckodag.** Den låg först i ett
  markdown-kort, men markdown-kort renderar tomt i den här Home
  Assistant-versionen – jag provade med statisk text och även den blev en tom
  remsa, så det är kortet som är trasigt och inte mallen. Brickor fungerar.
- Sju nya sensorer: `sensor.matsedel_mandag` till `sensor.matsedel_sondag`. Varje
  sensor plockar sin egen dag ur `days`-listan, så det spelar ingen roll vilka
  dagar som är valda. Tillståndet är rätten, och attributen visar erbjudandet den
  bygger på, priset, butiken och ingredienserna.
- Är dagen inte vald står det "Ej vald", och saknas rätt står det "Ingen rätt" –
  inget tomt fält att gissa om.

## 1.0.9

- **Veckans matsedel byggd på butikernas verkliga extrapriser.** De två knapparna
  `button.matsedel_generera_valda` och `button.matsedel_bygg_inkopslista` – de
  sista gula rutorna i gränssnittet – fungerar nu.
- Två kedjor är verifierade att svara **utan inloggning**:
  - **ICA**: erbjudandena ligger i sidans HTML, i `window.__INITIAL_DATA__` under
    `offers.weeklyOffers`. Butiksid 1003467 (Supermarket Borgholm) och 1004348
    (Maxi Kalmar).
  - **Willys**: `GET /axfood/rest/v1/search/campaigns/offline?q=<butik>&type=PERSONAL_GENERAL`.
    Butiksid 2285 (Kalmar Hansa City).
- **Coop är inte inkopplad.** Slutpunkten är identifierad men har inte gått att
  verifiera – webbläsaren blockerar den med CORS och utvecklingsmiljön når inte
  coop.se. I stället loggar tillägget vid start vad Coops API faktiskt svarar, så
  att den kan kopplas in när svaret är sett. Väljer man en Coop-butik säger
  sensorn `okand_butik` i stället för att visa något påhittat.
- **ICA:s `personalOffers` läses aldrig.** De är knutna till ett inloggat konto.
  Ett test vaktar det.
- Matsedeln paras ihop dag för dag: varje rätt bygger på en råvara som faktiskt är
  på extrapris den här veckan. Finns ingen sådan rätt kvar skrivs det ut i klartext
  i stället för att en rätt väljs på måfå och påstås vara ett fynd.
- **Recepten är en inbyggd lista** i `matsedel.py`, tjugo vardagsrätter. De hämtas
  inte någonstans ifrån och påstår sig inte göra det. Det är erbjudandena som är
  hämtade; kokboken följer med tillägget.
- Valet är deterministiskt utifrån veckonumret: samma vecka ger samma matsedel hur
  många gånger man än trycker.
- Två fällor i ordmatchningen, båda täckta av tester:
  - Svenska varunamn sitter ihop ("Laxfilé", "Kycklingfilé"), så råvaran måste få
    matcha början av ett ord. Kravet att träffen börjar vid en ordgräns hindrar
    ändå "Relaxdryck" från att bli fiskrätt.
  - ICA:s varugrupp används som filter, så "Laxermedel" under Hälsa & skönhet
    aldrig kan bli middag.
- ICA:s butikssökning tar parametern `query`. Med `q` **svarar** tjänsten men
  filtrerar inte – den ger slumpmässiga butiker som ser ut som ett resultat.
  Uppslagningen filtrerar därför träffarna en gång till.
- "Bygg inköpslista" skriver varorna i `todo.inkopslista` via `todo.add_item`.
  Restdagar bidrar inte med några ingredienser, och listan innehåller inga
  dubbletter.
- Ny modul `ha_api.py` som läser användarens hjälpare (vald butik, valda dagar,
  resterflaggan). Den skriver **aldrig** tillstånd med `POST /api/states` – det
  var mönstret som gjorde att ett trettiotal entiteter försvann vid varje omstart.

## 1.0.8

- **Poddknapp för mobilen.** Ny `button.podd_skicka_till_mobilen` och
  `sensor.podd_mobil_spelare`. Trycker du på knappen publiceras det senaste
  avsnittets titel, program, längd och `audio_url`, och automationen
  "Podd: Spela i Mobilen Notis" – som väntat på exakt den sensorn utan att den
  funnits – skickar en notis med spellänk.
- Sensorn ligger på ett **eget** MQTT-ämne, `jamjo/podcasts/mobile/state`, och
  uppdateras bara när knappen trycks. Hade den speglat poddflödet hade varje nytt
  avsnitt gett två notiser, eftersom "SR Play: Snabbnotis" redan larmar om nya
  avsnitt.
- **Rättar en tyst bugg i kommandohanteringen.** `add_command_handler`
  prenumererade bara via `on_connect`. En hanterare som registrerades efter att
  anslutningen var uppe blev därför aldrig prenumererad, och knappen hade varit
  död utan att något syntes i loggen. Nu prenumereras ämnet direkt om klienten
  redan är ansluten.

## 1.0.7

- **Målnotiser för Kalmar FF och Kalmar HC.** Ny modul som läser SVT Texts
  målservice, sidorna 377–383. De uppdateras löpande under pågående matcher och
  har formatet `Sirius       - Häcken       X - X 19:00`, där `X - X` betyder att
  matchen inte startat. Hockeymatcher har dessutom en periodrad, `(1-1,1-2,0-0)`.
- Fyra nya entiteter: `sensor.kalmar_ff_live_match_score`,
  `sensor.kalmar_hc_live_match_score`, `sensor.kalmar_ff_pagaende_match` och
  `sensor.kalmar_hc_pagaende_match`. Ställningssensorn innehåller **bara
  siffrorna**, så dess tillstånd ändras när ett mål görs och inte när matchtexten
  ändras. Automationen `Kalmar FF: Mål-notifiering i realtid` letade redan efter
  exakt `sensor.kalmar_ff_live_match_score` men entiteten har aldrig funnits.
- Sporten avgörs av rubriken ovanför raden, aldrig av gissning, och lagnamn
  matchas som helt ord. Det är samma två regler som infördes i 1.0.4 efter att
  "Kalmar" matchat innebandyklubben "Kalmarsund".
- Pollningen anpassar sig: var 30:e minut när inget Kalmarlag står på
  målservicen, var 5:e minut när en match är inlagd men inte startat, och varje
  minut medan den pågår – och då bara den sida matchen står på, inte alla sju.
  Flyttar matchen till en annan sida läses allt om.
- Femton tester mot verkligt sidinnehåll. Två format som en första version
  missade fångades av en genomkörning mot alla sju sidorna: långa lagnamn får
  bara ett mellanslag före ställningen (`Preussen Mün - Karlsruher S X - X 18:00`),
  och fakta-/målskyttesidorna upprepar samma lagpar utan avsparkstid. Utan
  skyddet blev `Sirius` en "sport" och nästa match fick fel sportetikett.

## 1.0.6

- **Nu syns det vilken apparat som räknas som gäst.** 1.0.5 filtrerade rätt – bara
  klienter på gästnätet räknas – men publicerade bara antalet. Stod husets egen
  Chromecast på gäst-SSID:et räknades den, och det gick inte att se härifrån.
  Närvarosensorn har fått tre nya attribut: `lillstugan_guest_macs`,
  `lillstugan_clients` (MAC, nät, signalstyrka, aktiv) och
  `lillstugan_ignored_macs`.
- **Ny inställning `guest_ignore_macs`.** En kommaseparerad lista med MAC-adresser
  som aldrig räknas som gäster, oavsett vilket nät de sitter på. Tänkt för husets
  egna apparater som står kvar på gästnätet. Skiftläge spelar ingen roll.
- **Fondsensorerna har fått tillbaka `state_class: measurement`.** När den togs
  bort i en tidigare version klagade Core på "state_class removed" för tio
  entiteter, eftersom det fanns gammal långtidsstatistik utan matchande sensor,
  och nya grafer slutade byggas. Alla fondsensorer med enhet får nu statistik
  igen. (Ärendet `units_changed` för `fondportfolj_daily_change_sek` måste
  fortfarande kvitteras i Inställningar – det gäller enheten på historiken.)
- Nio tester för gästlogiken, bland annat att en Chromecast på gästnätet går att
  undanta utan att en riktig gäst faller bort, och att samma apparat på två band
  räknas en gång.

## 1.0.5

- **Ny bokningshämtning från Stugsommar.** Stugans bokningskalender läses från
  samma slutpunkt som sajtens egen kalender använder,
  `POST /pubweb/vishus12/calendar?houseid=65555&hotel=0&calendar=ÅÅÅÅMM`. Varje
  dag har en klass som säger om den är ledig (`_a`, `_b`, `_f`), upptagen (`_x`)
  eller utanför den bokningsbara perioden (`_n`), och `_z` markerar säsongens
  sista dag.
- Modulen skiljer **stängd säsong från bokning**. Efter säsongens slut är varje
  dag markerad som upptagen ända tills nästa års kalender publiceras – för hus
  65555 gäller det allt efter 3 oktober 2026. De dagarna räknas inte som
  bokningar. Utan den skillnaden hade sensorn påstått en bokning på tretton
  månader.
- Fyra nya entiteter: `sensor.lillstugan_stugsommar_bokningar`,
  `sensor.lillstugan_nasta_bokning`, `sensor.lillstugan_sasongsslut` och
  `binary_sensor.lillstugan_uthyrd`. Svarar inte Stugsommar blir statusen `fel`
  och sensorn "Okänd" – aldrig ett påhittat datum.
- Den gamla `stugsommar.py` är borttagen, inte inkopplad. Den returnerade
  13–15 augusti 2026 som fast sträng oavsett vad sajten svarade.
- Gästnärvaron i Lillstugan räknade **alla** klienter som satt på Puck 2, även
  husets egna apparater. En Chromecast på det privata nätet rapporterades som
  gäst. Nu räknas bara klienter på gästnätet.
- Nya attribut på närvarosensorn: `lillstugan_clients_per_network` visar hur
  många klienter som sitter på varje nät, och `lillstugan_guest_network_detected`
  visar om OpenWrt kunde namnge näten. Går det inte att skilja näten åt behålls
  det gamla beteendet, men attributet avslöjar det i stället för att dölja det.
- Tobias telefon räknas fortfarande aldrig som gäst, och samma enhet på två band
  räknas som en.

## 1.0.4

- Rättar att Kalmar HC visade **innebandy** som hockey. Söksträngen "Kalmar"
  matchade klubben "Kalmarsund" på SVT Text sida 352, som är Innebandy SSL.
  Sensorerna visade en tabellplats och en kommande match från fel sport.
- En sida godkänns nu bara om dess rubrik nämner rätt sport.
- Lagnamn matchas som helt ord i stället för delsträng, så "Kalmar" aldrig
  matchar "Kalmarsund".
- Kalmar HC visar därmed "Okänd" fram till att hockeysäsongen startar och SVT
  publicerar en hockeytabell. Det är korrekt: säsongspremiären är i september.

## 1.0.3

- Ny sportbevakning byggd på SVT Text-TV via `api.texttv.nu`. Tabellplacering,
  nästa match och senaste resultat för Kalmar FF hämtas på riktigt från sida 343
  och 344. Hittas inte laget, eller svarar inte sidan, blir sensorn "Okänd" –
  aldrig ett påhittat värde.
- Kalmar HC söker igenom SVT:s hockeysidor och visar "Okänd" fram till
  säsongspremiären, i stället för en fryst tabell från förra säsongen.
- Ny soptömningsmodul som hämtar verkliga tömningsdatum från Borgholm Energi
  för den fastighet som anges i inställningarna. Visar nästa datum, vilka kärl
  det gäller och antal dagar kvar.
- Ny besökslogg för Lillstugan som bygger på faktisk gästnärvaro på Puck 2.
  Historiken sparas under `/data` och överlever omstarter. Korta passager
  filtreras bort och avbrott under 45 minuter räknas som samma vistelse.
- Den gamla `sports.py` är helt ersatt. Den skrev tillstånd via
  `POST /api/states` och innehöll hårdkodade matcher, poäng och resultat.
- Den gamla `stugsommar.py` är borttagen. Den returnerade fasta bokningsdatum
  oavsett vad DanCenters sida faktiskt sa.
- Startknapp för diskmaskinen tillagd i `jamjo_native.yaml`, byggd på
  `home_connect.start_selected_program`.

## 1.0.2

- Hastighetstest köas i stället för att lämna puckar låsta i "Mäter...".
  Tryck på flera puckar efter varandra fungerar nu; de körs ett i taget och
  visar "Köad" med sin plats i kön under tiden.
- Kön töms även när ett test misslyckas, så ingen puck kan fastna i mätläge.
- Fondens dagsutveckling blir "okänd" i stället för ett påhittat tal när
  kursförändringen inte går att läsa från kurskällan.
- Tar bort de hårdkodade reservvärdena för fondkurs, kursdatum och
  dagsutveckling i Avanza-grenen. Saknas kursen rapporteras ett fel i stället
  för en uppdiktad siffra.

## 1.0.1

- Publicerar fondens dagsutveckling i procent och kronor. Värdena beräknades redan
  i `funds.py` och skickades på MQTT, men saknade discovery-definition och blev
  därför aldrig till entiteter.
- Kopplar in poddbevakningen. `podcasts.py` fanns i paketet men importerades inte
  av något, så modulen kördes aldrig. Nu hämtas senaste avsnitt från Sveriges
  Radios API var 30:e minut och publiceras via MQTT discovery.
- Poddar utan verifierbar källa fylls inte längre med platshållartext. Ett program
  som inte svarar rapporteras i loggen i stället för att få ett påhittat avsnitt.
- Sveriges Radios `/Date(...)/`-tidsstämplar konverteras till ISO 8601.

### Nya entiteter

- `sensor.fondportfolj_daily_change_percent`
- `sensor.fondportfolj_daily_change_sek`
- `sensor.fond_<fond_id>_daily_change_percent` och `_daily_change_sek`
- `sensor.podd_sr_p3_dokumentar`, `sensor.podd_sr_ekot`, `sensor.podd_sr_usapodden`,
  `sensor.podd_sr_p1_dokumentar`, `sensor.podd_sr_sommar_i_p1`, `sensor.podd_sr_p3_krim`
- `sensor.senaste_poddavsnitt` och `sensor.podd_uppdaterad`

### Inte åtgärdat, medvetet

`sports.py` och `stugsommar.py` kopplades **inte** in. Modulerna innehåller
hårdkodade matchresultat, tabellplaceringar och bokningsdatum, och skriver dessutom
via `POST /api/states`, vilket ger entiteter som försvinner vid omstart. Att aktivera
dem som de ser ut skulle publicera påhittad data. De behöver skrivas om mot en
verklig källa först.

## 1.0.0

- Flyttar permanent drift till Home Assistant OS och använder Supervisor MQTT-service.
- Tar bort beroendet av en MQTT-användare och ett MQTT-lösenord i appens inställningar.
- Skapar verklig Tobias-närvaro, RSSI och ansluten puck från OpenWrt-stationstabellerna.
- Skapar verklig gästnärvaro i Lillstugan från anslutna klienter på Puck 2.
- Behåller befintliga `unique_id` och `entity_id` för nätverk, fonder och hastighetstester.
- Rättar enheter till `Mbit/s`, tidsstämplar till ISO 8601 och felaktig state class för pengar.
- Lägger till MQTT-availability så entiteter visar `unavailable` vid ett riktigt driftavbrott.
- Skapar en unik beständig OpenWrt-nyckel under `/data` och verifierar den på alla fyra puckar.
- Samlar alla tidigare saknade källmoduler i HAOS-appens paket.

## 0.5.1

- Använder samma svenska testserver i Växjö för jämförbara resultat från alla wifi-enheter.
- Provar automatiskt en andra svensk Växjö-server om den första inte ger ett komplett resultat.
- Undviker den felaktiga automatiska serverplaceringen i Ballerup.

## 0.5.0

- Manuell internetmätning från var och en av de fyra OpenWrt-enheterna.
- Visar nedladdning, uppladdning, svarstid, jitter, status och senaste mättid i Home Assistant.
- Endast ett bandbreddstest kan köras åt gången för att skydda 5G-anslutningen.
- Fasta MQTT-kommandon och förutsägbara entitets-ID:n för kontrollpanelen.

## 0.4.1

- Visar platser och roller i stället för de tekniska namnen puck1–puck4.
- Behåller interna MQTT-ID:n oförändrade så att historik och automationer fortsätter fungera.
- Visar begripliga mål och åtgärder i AI-förslagen.

## 0.4.0

- Byter standardmodell till `gpt-5.4-nano`.
- Kräver strukturerat AI-svar med en strikt lista över tillåtna förslag.
- Lägger till MQTT-entiteter för åtgärdsförslag, godkännande och avvisning.
- Utför aldrig en AI-föreslagen omstart utan ett separat knapptryck i Home Assistant.
- Lägger till 30 minuters giltighetstid och sex timmars spärr per puck.
- Blockerar SSH-åtgärder för offline-puckar och använder endast ett fast omstartskommando.

## 0.3.0

- Lägger till manuell, skrivskyddad nätverksanalys via OpenAI Responses API.
- Publicerar en MQTT-knapp och en analysentitet i Home Assistant.
- Skickar endast sammanfattade puckvärden utan MAC-adresser eller hemligheter.
- Begränsar analys till högst en gång per tio minuter som standard.

## 0.2.5

- Skickar firmwarens obligatoriska AD-skrivskydd med en ny RD-utmaning.
- Använder AD även vid utloggning så att ZTE-sessionen stängs korrekt.

## 0.2.4

- Använder ZTE:s GSM7-kodning för de fasta ASCII-varningstexterna.
- Visar routerns resultatkod om ett SMS-kommando avvisas.
- Loggar ut från ZTE efter varje SMS-försök så att webbsessionen inte lämnas låst.

## 0.2.3

- Skapar ZTE-webbsession innan SHA-utmaningen hämtas.
- Skickar samma AJAX-huvuden som routerns eget webbgränssnitt.
- Avvisar tom eller ogiltig inloggningsutmaning innan lösenordsförsök.

## 0.2.2

- Använder MC801A-firmwarens dubbla SHA-256-inloggning med lokal utmaning.
- Visar tydligare fel för fel lösenord, låsning och samtidig webbsession.

## 0.2.1

- Lägger till valfri, lokalt spärrad SMS-brygga för ZTE MC801A.
- SMS är avstängt som standard och accepterar bara fasta kommandon.
- Förenklar installationsbygget för Raspberry Pi 3.
- Kör Python utan buffring så att appens logg visas direkt.
## 0.6.0

- Ny separat fondportfölj med MQTT-sensorer för investerat belopp, värde och avkastning.
- Stöd för flera köp i samma fond och flera framtida fonder.
- Hämtar NAV och kursdatum från fondbolagets angivna fondsida var sjätte timme.
- Knapp i Home Assistant för att uppdatera fondkurser direkt.
# 1.0.57

- Rättar versionsmigreringen så gamla nyhetsposter och aviseringsposter verkligen rensas.

# 1.0.56

- Säkerställer att även internationella rubriker visas på svenska när en källa lämnar en engelsk rubrik.
- Rensar tidigare oöversatta nyhetsposter när den nya språkregeln tas i bruk.

# 1.0.55

- Delar nyhetsurval och trendförklaringar i separata strukturerade OpenAI-anrop.
- Avvisar ett trendresultat om modellen inte har förklarat samtliga sex inskickade trender.
- Tar bort en motsägelse i instruktionen som kunde göra viralsidan tom.

# 1.0.54

- Kräver en förklaring för sex aktuella Google Trends-poster i varje full analys så viralsidan inte blir tom.
- Visar en osäker trend med låg säkerhet men tillåter aldrig att den aviseras.
- Rensar äldre oöversatta poster en gång när de nya språkreglerna tas i bruk.

# 1.0.53

- Kräver svenska rubriker för vanliga internationella nyheter samtidigt som virala namn bevaras i original.
- Bedömer virala poster efter belagd trendstyrka i stället för personlig nyhetsrelevans.
- Kräver ett separat AI-beslut om att en post är aviseringsvärd; hög viralitet ensam kan aldrig ge en notis.

# 1.0.52

- Förhindrar avklippta OpenAI-svar när många internationella nyheter analyseras samtidigt.
- Ber modellen returnera endast poster som klarar visningsgränsen och ökar utrymmet för strukturerad JSON.

# 1.0.51

- Ger virala trender en separat, lägre visningsgräns utan att sänka den höga aviseringsgränsen.
- Balanserar urvalet mellan svenska nyheter, internationell politik, teknik, ekonomi och trendkällor.
- Lägger till BBC World, Technology och Business som internationellt underlag som OpenAI sammanfattar på svenska.

# 1.0.50

- Lägger till en konfigurerbar personlig nyhets- och trendbevakning i Home Assistant.
- Hämtar offentliga flöden från SVT, Ekot, Google Trends och Wikimedia och låter OpenAI välja bort brus.
- Bevarar virala termer i original men skriver sammanfattning, förklaring och svensk kontext på svenska.
- Publicerar endast poster med hög säkerhet som aviseringskandidater och begränsar antalet per dygn.
