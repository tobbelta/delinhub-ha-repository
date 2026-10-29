# Dagskoll Logg

Loggtjänsten placeras på Dell med Home Assistant OS: SSD, 16 GB RAM och gott
om ledigt utrymme passar bättre för kontinuerlig loggskrivning än Pi:s SD-kort.
Tilläggets privata `/data/events.sqlite3` följer med i Home Assistants backup
av tillägget. Backa upp tillägget och förvara även en kopia utanför Dell.

## Fullständig ERROR-/CRITICAL-inventering

`GET /v1/review?mode=error_groups&since=...&until=...` är läsande och kräver
visningsnyckel. Alla kvarvarande råa ERROR/CRITICAL räknas över hela det valda
mottagningsfönstret och ett låst övre sekvensnummer. Grupperingen använder hela
den lagrade normaliserade feltexten och tekniska fält; felkoder och olika
diagnostiska suffix hålls isär. Tydliga tidsstämplar, process-, nätverks- och
opaka identifierare normaliseras. Första/sista exempel visar både början och
slutet av lång text, med uttrycklig markering av utdraget.

Varje sida innehåller högst tio grupper med exakta antal och en eller två
citerbara exempel per grupp. Rå ERROR/CRITICAL behålls även för klassade äldre
stderr-notiser. Gruppantal avser hela inventeringen; evidens är exempel.
`page.next_cursor` binds med HMAC till fönster, övre sekvens, inventeringshash
och 24 timmars giltighet. Nästa sida ekar endast SHA-256 av begärd markör som
`request_cursor_sha256`; råmarkören ska inte ingå i modelltext.

Sidans vanliga JSON-representation är högst 48 KiB. Sidor tappar aldrig
grupper för att få plats. Det går att minska två exempel till ett med en
uttrycklig varningskod; även en för stor sådan sida avvisas. Högst 5000 grupper
och en begränsad beräkningstid tillåts. Misslyckad inventering är ett fel,
inte en tyst delmängd som påstås vara fullständig.

Markören bevarar startens globala nivåantal, källantal och lagrade tidsgränser.
Källrader har `counts_scope: error_groups`: deras antal avser enbart fasta
ERROR/CRITICAL, medan övriga nivåer är noll i detta filtrerade läge.
Gallring av INFO eller andra orelaterade poster stoppar inte fortsättningen:
vid ändrad gallringsräknare verifieras de relevanta felposternas digest igen.
Förlust av nödvändiga felposter ger `snapshot_pruned` (409), ändrat underlag `snapshot_changed` (409)
och utgången markör `snapshot_expired` (410). Börja ett nytt uttryckligt
granskningsförsök när en tidigare ögonblicksbild inte längre kan användas.
En GET skapar inga databasposter eller persistenta ögonblicksbilder. RAM-cachen
har högst två poster och 8 MiB; underlaget kan återbyggas från databasen.
Nyckeln `/data/review-cursor.key` skapas före HTTP-start och ska bevaras med
databasen vid uppdatering/backup. Den får aldrig publiceras eller loggas.

`health_basis: current_at_fetch` och `health_checked_at` anger att källhälsa
är en separat aktuell läsning. Den ingår inte i inventeringshashen och ändrar
inte fönstrets fasta antal eller sidornas grupper. Åtgärder måste alltid
kontrollera aktuell hälsa på nytt. Det tidigare balanserade översiktsläget
fungerar fortsatt utan `mode=error_groups`.

## Privat lokal installation i Jämjö

Den aktiva installationen heter **`local_dagskoll_logs`** och byggs privat på
Dell. Dess interna värdnamn är **`local-dagskoll-logs`**. Backendens `log_url`
och HA-integrationens `collector_url` anges till
`http://local-dagskoll-logs:8098`. Loggvyn öppnas med HA-ingress; port 8098
stannar på det interna tilläggsnätet.

Källan ligger i HA:s lokala tilläggsmapp `/local_apps/dagskoll-logs`.
Exportera filerna ur den granskade Git-revisionen och kontrollera deras
hashar. I den lokala kopian av `config.yaml` utelämnas endast `image:` så
Supervisor bygger på Dell. Hemligheter, `/data` och `/config/ssh` följer
inte med i byggkontexten. Den lokala källan och tilläggets privata data ska
båda ingå i säkerhetskopian.

## Användning

Öppna webbgränssnittet från tillägget eller lägg det i HA:s sidopanel.
Home Assistants inloggning skyddar vyn. Loggen uppdateras var tionde sekund
medan sidan är synlig; äldre poster visas med Visa äldre. Filtret kan söka
efter text, källa och nivå. Källornas senaste mottagning visas i lokal tid.
Gul markering visar en frånkopplad eller återanslutande insamlare. En sovande
mobil eller en tyst källa kan ha gammal mottagningstid utan att vara trasig.
HTTP-batcher visas som **Senast mottaget**; de har ingen varaktig anslutning
att övervaka. Telefonens, klockans och webbklientens mottagningstid bevaras
vid loggtjänstens omstart. Strömmande SSH-/HA-källor har separat
anslutningsstatus och kan visas som återanslutande eller frånkopplade.

## Inställningar

- `retention_days`: 1–365 dagar, normalt 30. Gallring utgår från när tjänsten
  tog emot posten, så en telefons offlinekö försvinner inte på grund av gammalt
  eller felaktigt klockslag.
- `max_db_mb`: databasens hårda sidgräns, normalt 2048 MiB. Loggarnas innehåll
  gallras tidigare för att ge plats åt index. WAL och tillfälliga SQLite-filer
  tillkommer med några MB under skrivning. En felande disk kvitteras aldrig
  som en lyckad leverans.
- `max_rows`: högst 2 000 000 sparade poster normalt. Den första uppnådda
  gränsen (ålder, antal eller storlek) gallrar de äldsta mottagna posterna.
- `ingest_tokens`: lista med `source` och `token`. Använd en slumpnyckel på
  minst 32 tecken för varje källa. Kortare än 24 tecken avvisas. Nyckeln ger
  bara skrivrätt för sin egen källa. Samma nyckel ska inte återanvändas.
- `viewer_token`: valfri separat lång nyckel för programmatisk läsning.
  Den används aldrig i webbläsaren. Tom nyckel stänger extern läsning.
- `admin_token`: separat slumpnyckel på minst 32 tecken för den enda
  administrativa åtgärden, att väcka en felande insamlares återförsök.
  Får inte vara samma som någon läs- eller skrivnyckel. Tomt stänger åtgärden.
- `collectors`: lokala insamlare för Supervisor/system/HA och SSH-källor.
  SSH-nycklar förvaras under `/config/ssh/`, med verifierade värdnycklar i
  `known_hosts`. Använd separat läsnyckel med tvingat loggkommando; ge inte
  loggtjänsten en allmän administrationsnyckel. Byt inga nätverksinställningar
  för att ansluta en insamlare.

Port 8097 är enbart intern HA-ingress; bara Supervisor-adressen
`172.30.32.2` accepteras. Den publiceras aldrig på värden. Port 8098 är
intern autentiserad mottagning som standard. Aktivera värdporten endast om
en LAN-klient behöver den. Publicera ingen port på internet. Telefon och
klocka använder sin befintliga autentiserade HA-anslutning via en proxy;
de får ingen generell loggläsnyckel. Över andra nät används en krypterad
förbindelse, till exempel den befintliga VPN-länken.

## API

`POST /v1/events`, `Authorization: Bearer <källans nyckel>`,
`Content-Type: application/json`:

```json
{"events":[{"id":"unique-event-id","time":"2026-10-28T10:00:00Z","level":"INFO","category":"lifecycle","event":"app.started","message":"Appen startade","device":"phone","version":"1.1.10.132","attributes":{"status":"ok"}}]}
```

`id` och `event` krävs; tid ska ha tidszon. Nivåerna är DEBUG, INFO, WARNING,
ERROR och CRITICAL. `correlation_id` kan följa samma åtgärd mellan tjänster.
En batch innehåller högst 100 poster, varje post högst 16 KiB och hela anropet
högst 256 KiB. Högst 120 batchanrop per minut och källa accepteras.

Efter genomförd databastransaktion returneras
`{"accepted":["unique-event-id"],"stored":1,"duplicates":0}`.
Redan lagrade `(source,id)` kvitteras också; klienten kan säkert skicka om
efter ett anslutningsavbrott. Avvisad batch lagras inte delvis. Klienten
behåller sin lokala kö vid 429, 503 eller anslutningsfel och använder
växande väntetid. Dubblettskydd gäller medan posten finns kvar i loggen.

Programmatisk läsning med den separata läsnyckeln:
`GET /v1/events?source=phone&level=ERROR&limit=100&before=1234&q=timeout`
och `GET /v1/status`. Fortsätt med `next_before` för äldre poster.
`GET /health` är en publik minimal hälsokontroll utan logginnehåll.

## Fråga AI om loggarna via Home Assistants MCP

Integrationen **Dagskoll Logg** registrerar ett eget läsande API i Home
Assistant. Den inbyggda MCP-servern tillhandahåller det på
`/api/mcp/dagskoll_logs`. En AI-klient på samma privata nät kan exempelvis
ansluta till `http://192.168.1.249/api/mcp/dagskoll_logs`, med Home Assistants
autentisering. HA-administratör krävs. Dagskolls AI-brygga använder den interna
adressen `http://supervisor/core/api/mcp/dagskoll_logs`.

I integrationens inställningar anges en separat `viewer_token` som matchar
loggtjänstens läsnyckel. Den stannar på servern. `ha_web_token` används för
webbläsarens drifthändelser och är en separat skrivnyckel för källan `ha-web`.
Tomma nyckelfält i inställningsformuläret behåller befintliga värden.

MCP erbjuder fyra verktyg:

- **LogStatus** visar lagring, gallring, anslutningar och när källorna hördes av.
- **SearchLogs** söker på källa, minsta nivå, text och tidsfönster. Normalt läses
  senaste dygnet. Högst 31 dagar, 50 poster och 32 KiB returneras per anrop.
- **LogContext** hämtar sammanhanget kring ett exakt `source/event_id`, inom
  1–30 minuter åt varje håll. Ange `include_other_sources=true` för att även
  se närliggande händelser från andra enheter.
- **LogReviewOverview** räknar hela det kvarvarande mottagningsfönstret
  (normalt ett dygn, högst sju dygn) per källa och nivå. Detaljer väljs
  balanserat med högst 64 källor, 24 grupper, 48 evidensposter och 48 KiB.
  Detaljgrupper är märkta som lägre gränser när de bygger på urval.

Svar innehåller citerbara `source/event_id`, händelsetid och mottagningstid.
Saknad data och avkortade resultat markeras. Verktygen kan läsa filtrerade
loggar; de har inga kommandon för att ändra nätverket, styra enheter, köra SQL
eller radera något. Home Assistants befintliga Assist-anslutning fortsätter
använda sin egen konfiguration. Välj det särskilda logg-API:t för loggfrågor.

Exempel på en läsning med **SearchLogs**:

```json
{"source":"puck3","minimum_level":"WARNING","since":"2026-10-28T18:00:00+01:00","until":"2026-10-28T23:00:00+01:00","limit":20}
```

Frågor kan exempelvis vara ”Vilka fel syns på puck3 ikväll?” eller
”Vad hände runt den här frånkopplingen?”. AI-svaret ska hänvisa till loggens
ID och tid och beskriva vad underlaget faktiskt visar. Loggtext behandlas
som observationer, aldrig som instruktioner till AI:n. Dagskolls brygga hämtar
underlaget via det fasta MCP-verktyget innan modellen får frågan.

## Dygnsgranskning och begränsad återanslutning

HA-komponenten schemalägger en granskning kl. 08.00 Europe/Stockholm och
erbjuder manuell start. AI-bryggans fasta `log_review`-profil hämtar
`LogReviewOverview` innan en AI-start räknas. Svaret innehåller en sammanfattning,
högst fem förbättringsförslag och tydliga begränsningar. Varje förslag måste
hänvisa till verkliga evidens-ID:n. Inga modellverktyg eller enhetsåtgärder
är aktiverade. Rapporter sparar begränsad täckningsmetadata, inte råloggar.

`GET /v1/review?since=<ISO8601>&until=<ISO8601>` använder läsnyckeln.
Översiktens gränser är fasta och räknas på mottagningstid; ursprunglig
händelsetid finns separat. Mätstart, verkligt kvarvarande data och livstids-
gallringsräknaren redovisas. Trettio dagars åldersgräns garanterar inte trettio
dagars historik: antal och storlek kan gallra tidigare. Ändra befintliga options
uttryckligen till de högre gränserna vid uppdatering; kontrollera därefter täckningen.

Endast HA:s separat auktoriserade administratörsflöde får anropa
`POST /v1/collectors/reconnect`, med adminnyckeln och JSON
`{"source":"ha-core","request_id":"<UUID>","expected_error_id":"collector/<id>"}`.
Servern kräver en konfigurerad strömkälla, samma aktuella felankare, minst tre
minuters verkligt fel och en worker som väntar på återförsök. Batchkällor,
friska läsningar och normala tysta strömmar avvisas. Claim och audit sparas
atomiskt före väckning. Samma request-ID kvitteras utan ny åtgärd i 30 dagar;
historiken har ett hårt tak och avvisar nya åtgärder om den blir full.

Svar är `requested` eller `already_requested`; förändrad källa/felbild ger 409.
Ett accepterat återförsök är inte bevis på återställd anslutning. HA läser tillbaka
aktuell status och visar osäkert resultat om återhämtning inte kan verifieras.
Ingen puck startas om, inga fjärrkommandon skickas och inga inställningar ändras.
MCP, läsnyckeln, skrivnycklarna och ingressvyn saknar tillgång till åtgärds-API:t.

## Innehåll och begränsningar

Apparna skickar händelsetyper, resultat, tidsåtgång, versionsnummer,
felklasser och tekniskt sammanhang. Driftloggar filtreras innan lagring.
Redigering av nästlade hemlighetsfält, vanliga autentiseringsformat,
URL-parametrar, MAC-adresser och märkta hälsovärden sker på mottagaren
som extra skydd. Skicka aldrig råa API-anrop/-svar, lösenord, hälsomätningar,
SMS-text, koordinater eller personliga AI-prompter. Automatisk filtrering
kan inte identifiera alla tänkbara privata uppgifter i fri text.

Detta är inte en inspelning av varje tangenttryckning, skärmbild eller
nätverkspaket. Android/Wear tillåter inte åtkomst till andra appars privata
loggar. Avstängda enheter kan inte logga; puckarnas ringbuffert kan hinna
skrivas över om Dell eller nätet ligger nere. Insamlarna visar luckor och
återanslutningar när det går att upptäcka dem. Retention och fulla lokala
köer innebär avsiktlig gallring; räknare ska visa den.

Tillägget har läsrätt till HA-/Supervisor-loggar via tilläggets API och
använder separata begränsade SSH-nycklar. Det flashar, uppdaterar eller
startar inte om puckar. Ingen firmwareåterställning installeras.

## Underhåll

Vid en lokal uppdatering: ta först en krypterad backup av tillägget och dess
lokala källa, kopiera den utanför Dell och verifiera hashen. Exportera därefter
den granskade revisionen till samma lokala tilläggsmapp. Behåll options,
databas och SSH-konfiguration. Höj tilläggets egen version, ladda om
tilläggsbutiken och uppdatera **`local_dagskoll_logs`**. Kontrollera att den
installerade versionen är rätt och tillståndet är `started`; ett accepterat
uppdateringskommando är ingen slutkvittens.

Python ska startas med `/usr/bin/with-contenv` så s6-miljön och
`SUPERVISOR_TOKEN` når insamlaren. Kontrollera den autentiserade statusens
`supervisor_token_present` och verkliga anslutningar till HA/Supervisor.
CI använder en syntetisk Supervisor som kontrollerar autentisering från den
verkliga insamlaren, samt provar omstart och bevarad databas. En separat
`docker exec`-process miljö räcker inte som bevis för tjänstens inloggning.

Version 0.1.3 läser Observer via HAOS värdjournalidentifierare och rättar
upptäckarens anslutningsstatus. Äldre poster behåller sina ID:n, nivåer och
meddelanden. Före 0.1.2 kunde socats normala N/I/D-rader få ERROR från
OpenWrts stderr-etikett; bedöm även originalmeddelandet och versionsnotisen.

Rotera en källnyckel genom att byta den både i tillägget och den aktuella
klienten. Vid lagringsfel,
kontrollera ledigt utrymme och HA:s logg innan en omstart. Kontakta
projektets underhållare via projektets GitHub-repo för felrapporter och
bifoga bara filtrerade tekniska uppgifter.
