# Delin Hub

Appen läser status från de fyra OpenWrt-puckarna via SSH och publicerar mätvärden till Home Assistant genom MQTT.

Från version 1.0 hämtar appen MQTT-adress och tillfälliga tjänsteuppgifter direkt från Supervisor. MQTT-fälten från äldre versioner finns bara kvar för uppgraderingskompatibilitet och används inte längre.

Efter start ska loggen visa Kök, Lillstugan, Sovrum och Vardagsrum som `online`. Appen startar automatiskt igen efter omstart av Raspberry Pi.

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
