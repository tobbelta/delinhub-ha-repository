# Delin Hub

Appen läser status från de fyra OpenWrt-puckarna via SSH och publicerar mätvärden till Home Assistant genom MQTT.

Från version 1.0 hämtar appen MQTT-adress och tillfälliga tjänsteuppgifter direkt från Supervisor. MQTT-fälten från äldre versioner finns bara kvar för uppgraderingskompatibilitet och används inte längre.

Efter start ska loggen visa Kök, Lillstugan, Sovrum och Vardagsrum som `online`. Appen startar automatiskt igen efter omstart av Raspberry Pi.

## Nyheter, Skvaller och Trender på nätet

Från 1.0.66 finns tre separata flöden. Nättrender bygger på Know Your Meme
och Google Trends och visar betydelse, ursprung, illustrativa exempel och
osäkerheter. Skvaller hämtas från Nöjesbladet, BBC Entertainment och från
1.0.72 även Flashbacks aktuella kändis- och kreatörstrådar. Skvallerbedömningen
söker öppna kompletterande källor, inklusive Flashback och Reddit, och
prioriterar en relevant öppen länk framför en betalvägg. Länkar måste finnas
i sökverktygets verkliga resultat. **Fördjupa** gör en ny webbsökning.
Vanliga nyheter bedöms från RSS-underlaget. Från 1.0.76 gör även
nättrendbedömningen kompletterande webbsökning.
En söktopp är inte automatiskt ett nätfenomen.

Aktuella forumrykten får tas med utan mediebekräftelse, med **Obekräftat**
i rubrik och sammanfattning. Många läsare eller inlägg visar intresse och
gör inte ett påstående mer bekräftat. Flashbacks aktuella ämneslista filtreras
till kändisskvaller och videokreatörer: minst 10 läsare vid hämtningen och
20 svar totalt. Historiska svar redovisas inte som nya svar. Upp till tolv
trådar hämtas, varav ett balanserat urval delar skvallrets åtta AI-platser
med tidningskällorna per körning. En trådrubrik ensam räcker inte för en
sammanfattning av dess påståenden; aktuellt innehåll söks först.
Aktiva trådar kan ombedömas efter sex timmar, även om de tidigare valts bort.
Övriga källor fortsätter om Flashback inte kan läsas. Sökningen har högst
180 sekunders ordinarie väntetid och kan öka AI-kostnaden för nya skvallerposter.

Från 1.0.75 ingår dessutom r/popculturechats offentliga Reddit Hot-RSS som
egen källa. Högst tolv poster läses; bara poster från de senaste sju dagarna
med en direkt Reddit-länk kan bli kandidater. Gamla fastnålade poster och
odaterade poster tas inte med. Publiceringstid prioriteras framför en
senare uppdateringstid. Forumaktivitet och källstatus hålls isär även här;
RSS ger inga tillförlitliga siffror för röster och kommentarer.
Reddit delar det balanserade skvallerurvalet med övriga källor och aktiva
poster kan bedömas om efter sex timmar. Ett Reddit-fel hindrar inte de
övriga källorna från att hämtas.

Från 1.0.76 är Reddit r/OutOfTheLoop och Flashbacks aktuella trådar med
uttrycks-, meme- eller trendanknytning också egna trendkällor. Flashbacks
lista läses bara en gång för både skvaller och trender. Trådens starttid
gissas inte från hämtningstiden. Reddit-poster måste vara högst sju dagar
gamla. Trendurvalet delar tolv AI-platser mellan källorna och söker vidare
på Reddit, Flashback och relevanta originalkällor. Vanliga nyheter och
allmänna forumfrågor filtreras bort. Ett identifierbart nätfenomen och
en konkret förklaring krävs; en forumrubrik är inte ensam tillräcklig.
Uttryckets namn behålls, och osäker spridning eller okänt ursprung förklaras
separat. Forumkandidater kan ombedömas efter sex timmar.

Följda skvallerämnen får från 1.0.76 upp till tre prioriterade platser i
bedömningskön och visas före övriga poster. Den vanliga poänggränsen får
inte dölja sådana träffar, men avvisat material med noll poäng visas aldrig.
Det höjer inte uppgifternas källstatus och aktiverar inga pushnotiser.
När **Drake** finns bland följda ämnen avses rapparen Aubrey Drake Graham,
även kallad Drizzy. Namnlika personer som Drake Bell eller Nick Drake
prioriteras inte. r/Drizzys offentliga nya-inläggsflöde kompletterar övriga
källor. Dessutom görs en riktad webbsökning var sjätte timme även om
forumflödena inte svarar. Bevakningsfrågan är inte en nyhet: modellen måste
hitta aktuellt källunderlag eller utelämna posten. **Sluta följa: Drake**
stänger av denna extra källa och bevakning. Manuell ombedömning kan starta
en ny sökning tidigare. Bevakningen täcker tillgängliga källor, inte hela nätet.
Från 1.0.77 krävs också ett uttryckligt stöd för att det finns en konkret
händelse eller ett konkret rykte. ”Inget nytt hittat” visas inte som skvaller.

`news_include_gossip` styr det nya skvallerflödet. I HA finns
`switch.nyheter_skvaller`; `switch.nyheter_viralt` styr fortsatt nättrender.
Skvaller och trender delar `news_viral_relevance_threshold` (normalt 30).
Den äldre exakta standarduteslutningen `kändisskvaller` migreras en gång i
sparade inställningar. Egna sammansatta uteslutningar bevaras.

Alla kategorier får plats i historiken. Bara nyheter kan ge automatiska
pushnotiser; trender och skvaller visas i flödena. Modellen styrs av
`openai_news_model`, normalt GPT-5.4 mini.

## Börsmarknader

När `markets_enabled` är aktiverat hämtar appen svenska och stora
internationella börsindex var tionde minut. `markets_poll_minutes` kan ställas
mellan 5 och 60 minuter. Varje index publiceras med kurs, dagsändring i procent,
punktändring och källans kurstid. OMX Stockholm Benchmark GI och Cap GI används
som marknadsreferenser för Länsförsäkringar Sverige Index; fondens officiella
jämförelseindex är en anpassad variant och indexvärdena är därför inte fondens
NAV eller en exakt värdering av fonden. Varje värde märks som pågående,
fördröjd kurs eller senaste stängningskurs. Yahoo Finance är inte en garanterad
realtidskälla.

## Väder

När `weather_enabled` är aktiverat hämtar appen en tredygnsprognos från
Open-Meteo för Stugan och Nygatan. Tobias telefon kan publicera en position till
`jamjo/weather/phone_location/set`; koordinaterna avrundas till två decimaler
innan de lämnar telefonen och sparas som ett retained MQTT-meddelande. Därmed
fortsätter HA att uppdatera telefonens senaste plats även när telefonappen är
stängd. `weather_poll_minutes` kan ställas mellan 5 och 60 minuter.

Prognoserna publiceras som `sensor.vader_stugan`, `sensor.vader_nygatan` och
`sensor.vader_tobias_telefon`. Senaste giltiga prognos behålls och märks som
gammal vid ett tillfälligt källfel.

## Journalhistorik

Journalexporten från 1177 behandlas lokalt med `scripts/import_1177_journal.py`.
Importören skickar endast strukturerade värden för HbA1c, LDL, eGFR och
albumin/kreatinin. PDF-filen, journalanteckningar, beställare och vårdenheter
skickas inte vidare.

Den validerade sammanställningen sparas i appens beständiga lagring och
publiceras som `sensor.delin_hub_journalhistorik`. Sensorn är undantagen från
Home Assistants recorder-historik eftersom den redan innehåller sin egen
tidsserie. En ny journalexport importeras manuellt med:

```text
python scripts/import_1177_journal.py <sökväg-till-pdf>
```

## Privat DNA-rapport

Från 1.0.65 kan `jamjo/dna/command` med `{"action":"review"}` starta en
bakgrundsgranskning. Telefonens DNA-vy och den uppdaterade HA-dashboarden har
knappen **Granska igen**. Rapporten visar status, historik och forskningskällor.
Genotyper matchas lokalt; litteratursökning och AI-anrop gäller alltid samma
fasta offentliga markörurval och innehåller inga personliga genotyper.
Nya forskningsspår är obekräftade personliga tolkningar, inte beräknad risk.
En ny import behövs för markörer som inte fanns med i en äldre rapport.

Modeller väljs separat med `openai_network_model`, `openai_news_model` och
`openai_dna_model`. Tomt nätverksval använder det tidigare `openai_model`;
nyheter och DNA använder `gpt-5.4-mini` som standard. AI kräver befintlig
OpenAI-aktivering och API-nyckel. Utan AI fungerar omräkning och källsökning.

`scripts/import_dna_traits.py` läser en MyHeritage-råfil lokalt och använder
bara ett litet, fast urval markörer för vardagliga egenskaper. Den fullständiga
råfilen skickas inte till Home Assistant och ingår aldrig i Git eller appbilden.

Tolkningarna, använda markörer, säkerhetsnivåer och forskningskällor sparas i
appens privata lagring och publiceras som
`sensor.delin_hub_dna_egenskaper`. Resultaten är utbildande
sannolikhetsassociationer och ska inte användas som medicinska diagnoser.

```text
python scripts/import_dna_traits.py <sökväg-till-MyHeritage-csv>
```

## Närvaro

Tobias telefon identifieras med den MAC-adress som anges i `tobias_phone_mac`. Appen läser riktiga associerade wifi-klienter från alla fyra OpenWrt-puckar och publicerar närvaro, starkaste RSSI samt ansluten puck. Lillstugans gästnärvaro räknas från riktiga klienter på Puck 2 efter att infrastrukturens MAC-adresser har filtrerats bort.

## SSH-nycklar

SSH-nyckeln lagras endast som `/data/ssh/id_ed25519` i appens beständiga
lagring och följer därför med vid uppdateringar. Ingen privat SSH-nyckel finns i
Git eller i appbilden.

En helt ny installation skapar en unik nyckel och skriver den publika delen i
loggen. Den publika nyckeln måste då läggas i
`/etc/dropbear/authorized_keys` på puckarna. Befintliga installationer behåller
sin redan installerade `/data`-nyckel automatiskt.

## Internettest från varje wifi-enhet

Varje OpenWrt-enhet har en knapp **Testa internet nu** i Home Assistant. Testet körs på den valda enheten och visar nedladdning, uppladdning, svarstid och jitter. Endast ett test kan köras åt gången. Ett fullständigt test belastar 5G-anslutningen och kan tillfälligt påverka gäster och strömmande media.

Alla enheter använder samma svenska testserver i Växjö, med en andra svensk server som reserv, så att resultaten kan jämföras mellan byggnaderna.

## AI-analys och manuellt godkända åtgärder

OpenAI-analysen skickar endast summerade mätvärden utan puckarnas IP-adresser, MAC-adresser, SSH-nycklar eller lösenord. Standardmodellen är `gpt-5.4-nano`.

`ai_remediation_enabled` är avstängt som standard. När funktionen aktiveras kan AI:n bara föreslå `none` eller omstart av en namngiven puck. Förslaget visas i Home Assistant men utförs inte förrän användaren trycker på **Godkänn föreslagen åtgärd**.

Skydden är fasta och kan inte ändras av modellen:

- förslaget upphör efter 30 minuter;
- samma puck har sex timmars åtgärdsspärr;
- en offline-puck kan inte startas om via SSH;
- endast puckar i den lokala inventeringen kan väljas;
- ett fast, förprogrammerat omstartskommando används;
- **Avvisa föreslagen åtgärd** raderar väntande förslag utan att köra något.

En helt offline puck kräver fysisk omstart eller ett separat smartuttag. Funktionen är inte automatisk självläkning; den är ett human-in-the-loop-flöde.
# Personliga nyheter och trender

Under **Konfiguration** kan du ändra intresseprofil, ämnen som ska väljas bort,
om vanliga nyheter och virala trender ska ingå, separata trösklar för vanlig
nyhetsrelevans och viral visning samt den högre tröskeln för
avisering, högsta antal nyhetsaviseringar per dygn samt tyst tid. OpenAI måste
vara aktiverat och ha en API-nyckel. Offentliga rubriker och trendunderlag
skickas för bedömning; lösenord, HA-data och hälsovärden ingår inte.


## Uppdrag och historik från 1.0.67

Källorna körs som separata jobb. Varje jobb kan bara ha en aktiv körning;
en ny begäran under arbetet köas till nästa körning. Fel bevarar senaste
lyckade tid och försöks igen inom högst en minut. `sensor.delin_hub_uppdrag`
publicerar jobb, senaste 50 kommandon, 100 händelser, 90 hälsodagar och
AI-användning. Historiken ligger i `/data/hub.json`. Core-paketet undantar
den stora statusentiteten från Recorder.

Telefonens Systemkontroll och HA-dashboarden Delin Hub visar status.
Kör igen begär ett nytt källjobb. DNA startas i Hälsa → DNA. Nyheter har
separata val för att hämta nytt och bedöma om. Gränser filtrerar upp till
300 sparade bedömningar från sju dagar utan ett nytt AI-anrop. Ordlistan
behåller upp till 100 poster. Följda termer prioriteras i det befintliga
källurvalet; detta är inte en fullständig bevakning av alla sociala medier.

Glookos `glooko_glucose_unit` ska motsvara kontots glukosenhet (mmol/L i
denna installation). Avslutade dygn hämtas för senaste veckan och sparas
centralt. Veckobilden kräver minst fyra matchade dagar, glukostäckning på
minst 70 procent och steg rapporterade efter kl. 21. Skillnader beskrivs
som observationer, aldrig som orsak eller behandlingsråd. Steghistoriken
kräver fortfarande synk från telefonen. Pumpens senaste synk visas separat
från tidpunkten då rapporten hämtades.

Installationsmanifestet genereras med `scripts/installation_manifest.py
--write`. `--check --record` jämför installerade Core-filer, dashboards och
tillägg samt läser Android-releasen. Telefon och klocka rapporterar sina
observerade versioner separat. Fysisk kontroll och en fullständig
provåterställning ingår inte i denna automatiska kontroll.
