# Driver Sign-up Bot — JavaScript / Railway

Dit is de JavaScript-versie van je driver sign-up bot. Je hoeft **geen Python of Node.js op je eigen pc** te installeren: Railway installeert en draait alles online.

## De bot kan dit

- Een openbaar **Driver sign-up**-paneel plaatsen met `/plaats_signup_panel`.
- Drivers de volledige aanmelding via DM laten invullen: driver number, nationaliteit, EA ID, fulltime/reserve en drie rondetijden.
- Drie tijden zoals `1:23.456` optellen en automatisch hun gemiddelde tonen.
- De aanmelding alleen naar een gekozen staff-kanaal sturen.
- Staff laten kiezen tussen **F1** en **F2**, of afwijzen met reden.
- Bij goedkeuring de F1- of F2-rol geven en een DM sturen naar de driver.
- Alle afgehandelde aanvragen naar het gekozen archiefkanaal sturen.
- Instellingen en wachtende aanmeldingen bewaren, ook na een herstart van de bot.

## Stap 1 — Discord voorbereiden

1. Maak een bot in de [Discord Developer Portal](https://discord.com/developers/applications).
2. Onder **Bot**: zet **Server Members Intent** aan.
3. Nodig de bot uit met de OAuth2-scopes `bot` en `applications.commands`.
4. Geef hem minimaal de permissies View Channels, Send Messages, Embed Links, Read Message History en Manage Roles.
5. Zet de rol van de bot in Discord **boven** je F1- en F2-driverrollen.
6. Maak deze kanalen:
   - `#driver-sign-up` — publiek; hierin komt de knop.
   - `#pending-driver-signups` — alleen staff; hierin komen open aanmeldingen.
   - `#archived-driver-signups` — alleen staff; hierin komen afgehandelde tickets.

Maak de twee staff-kanalen zelf privé voor `@everyone`. Daarin staan EA ID's en rondetijden; de bot verandert Discord-kanaalrechten niet automatisch.

## Stap 2 — Uploaden naar GitHub

1. Maak een gratis account op [GitHub](https://github.com/) als je die nog niet hebt.
2. Maak een nieuwe **private repository**, bijvoorbeeld `driver-signup-bot`.
3. Upload de inhoud van deze map naar die repository. Upload **niet** `.env` en deel je Discord-token nooit.

## Stap 3 — Online zetten op Railway

1. Ga naar [Railway](https://railway.app/) en log in met GitHub.
2. Kies **New Project** → **Deploy from GitHub repo** en selecteer de repository.
3. Open na de eerste deploy je service en ga naar **Variables**. Voeg toe:

   ```text
   DISCORD_TOKEN = jouw echte Discord bot-token
   ```

   Optioneel tijdens het testen, zodat slash commands meteen in één server zichtbaar zijn:

   ```text
   DISCORD_GUILD_ID = jouw Discord-server-ID
   ```

4. Voeg onder **Volumes** een volume toe en koppel het aan `/app/data`.
5. Voeg bij **Variables** ook toe:

   ```text
   DATA_DIR = /app/data
   ```

6. Railway herstart/deployt de service. In de logs moet je iets zien als `Ingelogd als ...` en `Slash commands klaar ...`.

Het volume is belangrijk: zonder volume verliest een cloudservice de opgeslagen configuratie en tickets bij een nieuwe deploy.

Railway kan een Discord.js-bot als blijvende gateway-worker draaien en gebruikt omgevingsvariabelen voor je geheime token. [Railway’s Discord.js-hostingpagina](https://railway.com/deploy/discordjs-bot)

## Stap 4 — De bot in Discord instellen

1. Voer `/setup` uit.
2. Kies met de knoppen:
   - staffrol;
   - F1-driverrol;
   - F2-driverrol;
   - kanaal voor wachtende aanmeldingen;
   - archiefkanaal;
   - kanaal voor het aanmeldpaneel.
3. Klik op **Status vernieuwen** en controleer dat alles ingevuld is.
4. Voer `/plaats_signup_panel` uit.

Daarna werkt de flow automatisch. Staff kan `/setup_status` gebruiken om de instellingen te bekijken.

## Wat een driver ziet

1. Klikt **Driver sign-up** in de server.
2. Krijgt een DM van de bot.
3. Vult profiel en drie tijden in.
4. Ziet meteen het gemiddelde en wacht op staff.
5. Krijgt na beoordeling een DM met F1 of F2; de bijbehorende rol wordt automatisch toegevoegd.

## Als iets niet werkt

- **Geen DM:** de driver moet tijdelijke privéberichten voor de server toestaan.
- **Rol wordt niet gegeven:** controleer Manage Roles en of de botrol hoger staat dan F1/F2.
- **Commands verschijnen niet:** zet tijdelijk `DISCORD_GUILD_ID` in Railway Variables en deploy opnieuw.
- **Aanmeldingen verdwijnen na een deploy:** controleer of het Railway-volume op `/app/data` is gekoppeld én `DATA_DIR=/app/data` staat ingesteld.
