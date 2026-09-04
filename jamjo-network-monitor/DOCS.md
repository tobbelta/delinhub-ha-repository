# Delin Hub

Appen läser status från de fyra OpenWrt-puckarna via SSH och publicerar mätvärden till Home Assistant genom MQTT.

Från version 1.0 hämtar appen MQTT-adress och tillfälliga tjänsteuppgifter direkt från Supervisor. MQTT-fälten från äldre versioner finns bara kvar för uppgraderingskompatibilitet och används inte längre.

Efter start ska loggen visa Kök, Lillstugan, Sovrum och Vardagsrum som `online`. Appen startar automatiskt igen efter omstart av Raspberry Pi.

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
om vanliga nyheter och virala trender ska ingå, trösklarna för visning och
avisering, högsta antal nyhetsaviseringar per dygn samt tyst tid. OpenAI måste
vara aktiverat och ha en API-nyckel. Offentliga rubriker och trendunderlag
skickas för bedömning; lösenord, HA-data och hälsovärden ingår inte.
