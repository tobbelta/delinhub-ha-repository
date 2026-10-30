## 0.3.2

- Fullständig felgranskning kan åter ge en kontrollerad återanslutningsknapp
  när den aktuella felposten är verifierad på samma sida och loggkällans
  serverstyrda återförsök får väckas. Friska eller tysta källor påverkas inte.
- Verkliga misslyckade insamlingsanslutningar klassas som ERROR så deras
  verifierade ankare ingår i felgranskningen. Tysta lästimeouter, vänteläge
  och planerade stopp får fortsatt normala nivåer.

## 0.3.1

- Säkerhetskopiering pausar endast loggappen under kopieringen och återstartar
  den därefter. Det ger en stabil SQLite-kopia och undviker att tillfälliga
  markörfiler försvinner medan arkivet skrivs.

## 0.3.0

- Nytt läsläge `error_groups` räknar samtliga kvarvarande råa ERROR/CRITICAL i
  ett låst mottagningsfönster och övre sekvensnummer, utan 400-radersgräns.
- Exakta grupper har stabila ID:n och först/sist-evidens. Hela normaliserade
  feltexten används; felkoder och diagnostiska suffix behålls. Äldre socat-
  stderr-notiser ingår med ursprunglig nivå och förklarande klassning.
- HMAC-skyddade markörer ger högst tio grupper per sida och gäller i 24 timmar.
  Förlust av nödvändiga felposter, ändrat underlag, utgång, tids- och
  storleksgränser ger uttryckliga fel. Orelaterad INFO-gallring tillåts när
  felinventeringens digest är oförändrad; startens globala antal bevaras.
- Markörnyckeln skapas enbart vid tjänstestart i privat `/data`; GET skriver
  ingen inventering till databasen. Begränsad RAM-cache kan återbyggas efter
  omstart. Aktuell källhälsa redovisas separat från den fasta inventeringen.

## 0.2.0

- Läsande dygnsöversikt räknar hela det kvarvarande mottagningsfönstret och
  ger ett begränsat, balanserat evidensurval för HA:s AI-granskning via MCP.
- Redovisar aktuell källhälsa, kö-/gallringsgränser och äldre klassningsartefakter
  separat. Tysta Supervisor-strömmar återöppnas utan falskt enhetslarm.
- Separat adminnyckel kan väcka en verifierat felande insamlares återförsök.
  Åtgärden har beständigt dubblettskydd och audit; inga enhetskommandon körs.
- Höjer standardgränserna till 2048 MiB och 2 miljoner poster. Befintliga
  installationers options måste ändras uttryckligen; faktisk täckning redovisas.

## 0.1.4

- Visar HTTP-batchkällor som Senast mottaget, med bevarad mottagningstid.
  En omstart gör inte tysta telefon-/klockkällor till felande anslutningar.
- Endast verkliga strömkällor markeras som återanslutande vid start.

## 0.1.3

- Hämtar Observer-loggen via HAOS värdjournal och dess stödda identifierare.
- Återställer upptäckarens status när tilläggsupptäckten åter fungerar.

## 0.1.2

- Startar Python med s6-miljön återställd så HA-/Supervisor-autentiseringen
  finns tillgänglig för de verkliga insamlarna.
- CI verifierar autentisering från den körande insamlaren med en syntetisk
  Supervisor-tjänst, utan att skriva några nycklar i testresultaten.
- Klassar socats egna N/I/D/W/E/F-nivåer före OpenWrts generella stderr-etikett.
- Visar numerisk HTTP-felkod för felande insamlare och en autentiserad
  statusflagga för närvaro av Supervisor-inloggning.

## 0.1.1

- Maskerar även sammansatta nyckelnamn som `mqtt_password`, `zte_admin_password`
  och `codex_access_token` i text från konfigurations- och valideringsfel.
- Hanterar citerade värden och escapade citattecken i Pythonrepr och JSON.

## 0.1.0

- Gemensam lokal SQLite-logg med hållbara kvittenser och dubblettskydd.
- Autentiserad mottagning med separata nycklar per källa.
- Sökbar vy i Home Assistant med källstatus och lagringsöversikt.
- Automatisk gallring och begränsad lagring, köer och anropsstorlek.
- Insamling av filtrerade HA-/systemloggar och händelser samt nätverksloggar via SSH.
- Läsande AI-/MCP-verktyg via Home Assistant med tidsavgränsning och citerbara händelse-ID:n.
