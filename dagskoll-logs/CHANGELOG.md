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
