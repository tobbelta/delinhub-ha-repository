# Dagskoll Logg

Gemensam lokal driftlogg på Dell för Dagskoll, Home Assistant, puckarna och Pi.
I Jämjö byggs och körs tillägget privat på Dell som **`local_dagskoll_logs`**.
Den interna adressen är **`http://local-dagskoll-logs:8098`**. Öppna loggvyn
inne i Home Assistant för sökning, filter och källornas anslutningsstatus.

Tjänsten lagrar strukturerade händelser och filtrerad driftinformation.
Lösenord, nycklar, autentisering, råa hälsovärden och meddelandeinnehåll hör
inte hemma i loggen. Loggning fungerar utan molntjänst eller abonnemang.

Tillägget har egen version, för närvarande **0.1.4**. Uppdateringar byggs från
granskad Git-källa i HA:s lokala tilläggsmapp. Privata nycklar och loggdata
ligger utanför avbildningen och bevaras vid uppdatering. CI provar autentisering
från den verkliga insamlarprocessen under normal containerstart.

HA:s separata MCP-adress `/api/mcp/dagskoll_logs` erbjuder läsande AI-verktyg
med citerbara händelse-ID:n. Telefon och klocka behöver apprelease 133 från
Dagskolls Appversioner för den nya apploggningen.

Se Dokumentation för privat lokal installation, uppdatering och begränsningar.
