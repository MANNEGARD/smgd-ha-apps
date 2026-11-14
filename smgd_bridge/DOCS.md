# SMGD Bridge – dokumentation

## Vad appen gör

SMGD Bridge kopplar ihop SMGD Developer Lab (molnet) med enheter i ditt hem. Den hämtar jobb
från molnet över en utgående HTTPS-anslutning och utför dem lokalt:

| Jobb | Vad bryggan gör |
|---|---|
| LG webOS-TV | Parkopplar i utvecklarläge, installerar/startar/stänger/avinstallerar appar, listar appar, samlar loggar |
| ESPHome | Skickar färdigbyggd firmware över nätverket (OTA) och kontrollerar att enheten kommer tillbaka |
| Test Lab | Släpper igenom anrop till lokala tjänster som en administratör har godkänt (LAN Gateway) |
| Test Lab, internet (valfritt, av som standard) | Gör en testsessions internetanrop från hemnätet – bara till publika adresser, aldrig till något lokalt |
| ESPHome Device Builder (valfritt, av som standard) | Håller enhetsfilerna i `config/esphome` och ESP32-projekten i SMGD Developer Lab lika, åt båda håll |

Bryggan kompilerar aldrig något och kör aldrig fria kommandon. Den utför bara en fast uppsättning
åtgärder, mot enheter som är registrerade i SMGD Developer Lab **och** ligger i de nätverk du
tillåter här.

## Installation

1. Öppna **Inställningar → Tillägg → Tilläggsbutik** i Home Assistant.
2. Välj menyn **⋮ → Förråd**, klistra in adressen till SMGD:s app-förråd och välj **Lägg till**.
   Adressen står i projektets `haos/README.md`.
3. Leta upp **SMGD Bridge** i butiken och välj **Installera**.
4. Öppna fliken **Konfiguration** och fyll i **API-adress**. Adressen visas i SMGD Developer Lab
   under **Enheter → Lägg till brygga**. Spara.
5. Starta appen. Låt **Starta vid uppstart** vara på.

## Parkoppling

1. Öppna appens flik **Logg**. Där står en rad med `PAIRING CODE: XXXX-XXXX` och bryggans
   fingeravtryck.
2. Öppna SMGD Developer Lab → **Enheter → Lägg till brygga** och skriv in koden.
3. Jämför fingeravtrycket som visas i webbläsaren med det i loggen. De ska vara lika.
4. Bryggan visas som **Online** inom några sekunder.

Koden gäller i tio minuter och bara en gång. Går den ut visar loggen en ny. Koden är det enda
bryggan skriver i klartext; åtkomstnycklar, TV-nycklar och lösenord skrivs aldrig i loggen.

## Inställningar

| Inställning | Betydelse |
|---|---|
| **API-adress** | Adressen till SMGD Developer Labs API. Måste börja med `https://`. |
| **Bryggans namn** | Namnet som visas i SMGD Developer Lab. |
| **Loggnivå** | `debug`, `info`, `warn` eller `error`. |
| **Tillåtna nätverk** | Nätverk i CIDR-form, t.ex. `192.168.1.0/24`. Tom lista betyder alla privata nätverk (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 m.fl.). Bryggan vägrar alltid adresser utanför listan – även om molnet ber om dem. |
| **ESP OTA aktiverat** | Stäng av om bryggan inte ska få uppdatera ESPHome-enheter. |
| **Max samtidiga jobb** | 1–8. Det körs aldrig mer än ett jobb per enhet. |
| **Extra nedladdningsvärdar** | Behövs normalt inte. |

Ändringar gäller efter omstart av appen.

## Behörigheter

Appen begär **endast** läsåtkomst till Home Assistants API (`homeassistant_api`). Den används för
att läsa enhetsregistret (bekräfta firmwareversion efter en uppdatering) och – bara för
testsessioner som en administratör godkänt – för LAN Gateway. Gatewayen är skrivskyddad om inte
en administratör uttryckligen godkänt enskilda tjänster och entiteter.

Appen begär **inte**: öppna portar, host-nätverk, privilegierat läge, Supervisor-API, Docker-socket
eller åtkomst till Home Assistants konfigurationsmappar.

## Lagring

Allt ligger i appens egen datakatalog (`/data`) och följer med vid omstart, uppdatering och
säkerhetskopiering av appen:

| Katalog/fil | Innehåll |
|---|---|
| `identities/` | Bryggans nycklar och åtkomstuppgifter (endast läsbara för appen) |
| `devices/<enhet>/` | TV-nyckel och låst värdnyckel för varje parkopplad TV |
| `journal/` | Pågående och avslutade jobb, så att en omstart aldrig upprepar en installation |
| `tmp/` | Tillfälliga filer; töms vid start |
| `status.json` | Aktuellt läge (används av hälsokontrollen). Innehåller inga hemligheter. |

Avinstallerar du appen och tar bort dess data måste bryggan parkopplas på nytt, och varje TV
parkopplas igen.

## Nätverk

- Bryggan når enheter i hemnätverket genom Home Assistants vanliga nätverk för appar.
- Registrera enheter med **IP-adress** (ge dem gärna fast adress i routern). Namn som slutar på
  `.local` fungerar bara om Home Assistant kan slå upp dem.
- LG-TV: utvecklarläget använder port 9922 (SSH) och 9991 (nyckelserver). ESPHome OTA: port 3232.

## Hälsokontroll

Containern har en inbyggd hälsokontroll: bryggan uppdaterar `status.json` hela tiden, och
kontrollen larmar om filen är äldre än två minuter eller om bryggan har stannat. Slå på
**Vakthund** för appen om Home Assistant ska starta om den automatiskt.

## Felsökning

| Symptom | Åtgärd |
|---|---|
| `api_url is required` / `api_url must use https` i loggen | Fyll i API-adressen under Konfiguration. |
| `cannot reach the API; retrying` | Kontrollera internetanslutningen och API-adressen. Bryggan försöker igen av sig själv. |
| `this bridge has been revoked or replaced` | Bryggan har återkallats i SMGD Developer Lab. En ny parkopplingskod visas i loggen. |
| TV:n visas som offline | Kontrollera att TV:n är på, att utvecklarläget är aktivt (det stängs av efter en tid) och att adressen stämmer. |
| "SSH-värdnyckeln har ändrats" | TV:n har fått en ny nyckel (t.ex. efter fabriksåterställning) – eller så svarar något annat på adressen. Granska och godkänn i SMGD Developer Lab. |
| "Adressen ligger utanför de nätverk som är tillåtna" | Lägg till nätverket under **Tillåtna nätverk**, om enheten verkligen ska nås. |

## Vad som inte är verifierat

Den här versionen är testad som container mot simulerade enheter. Den har **inte** körts på en
riktig Home Assistant OS-installation, mot en riktig LG-TV eller en riktig ESP32, och avbilden
för aarch64 har inte byggts. Aktuell status finns i projektets `IMPLEMENTATION_STATUS.md`.
