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
