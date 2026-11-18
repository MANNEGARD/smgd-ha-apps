# SMGD Bridge – dokumentation

## Vad appen gör

SMGD Bridge kopplar ihop SMGD Developer Lab (molnet) med enheter i ditt hem. Den hämtar jobb
från molnet över en utgående HTTPS-anslutning och utför dem lokalt:

| Jobb | Vad bryggan gör |
|---|---|
| LG webOS-TV | Parkopplar i utvecklarläge, installerar/startar/stänger/avinstallerar appar, listar appar, samlar loggar |
| ESPHome | Skickar färdigbyggd firmware över nätverket (OTA) och kontrollerar att enheten kommer tillbaka |
| Test Lab (valfritt, av som standard) | Släpper igenom anrop till lokala tjänster som en administratör har godkänt (LAN Gateway) |
| Test Lab, internet (valfritt, av som standard) | Gör en testsessions internetanrop från hemnätet – bara till publika adresser, aldrig till något lokalt |
| ESPHome Device Builder (valfritt, av som standard) | Håller enhetsfilerna i `config/esphome` och ESP32-projekten i SMGD Developer Lab lika, åt båda håll |
| Hemligheter i Device Builder (valfritt, av som standard) | På begäran av en administratör: flyttar lösenord i klartext till `secrets.yaml` (lokalt), och – bara om **Dela hemligheter med SMGD Developer Lab** är påslaget – lämnar ut värdena för de hemligheter en synkad enhet använder |

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
| **Tillåtna nätverk** | Nätverk i CIDR-form, t.ex. `192.168.1.0/24`. Tom lista betyder alla privata nätverk (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 m.fl.). Bryggan vägrar alltid adresser utanför listan – även om molnet ber om dem. En rad som inte är ett nätverk i CIDR-form gör att appen inte startar; loggen säger vilken rad det gäller. |
| **ESP OTA aktiverat** | Stäng av om bryggan inte ska få uppdatera ESPHome-enheter. |
| **LAN Gateway** | Av som standard. Slå på för att testsessioner i SMGD Developer Lab ska kunna nå godkända tjänster i hemnätet genom bryggan. Vilka tjänster bestäms i Developer Lab, men utan den här inställningen används gatewayen inte alls – molnet kan inte slå på den. |
| **Home Assistant via gatewayen** | `off` (standard), `read_only` (läsa godkända entiteter) eller `interactive` (även anropa godkända tjänster). Ett tak som sätts här; Developer Lab kan bara vara strängare. Kräver att LAN Gateway är påslagen. |
| **Internet för testsessioner** | Av som standard. Slå på för att en testsessions internetanrop (”Internet som på TV:n”) ska gå ut från hemnätet genom bryggan. Bara publika adresser nås den vägen – aldrig något i hemnätet – och varje värd loggas. |
| **Synk med ESPHome Device Builder** | Av som standard. Slå på för att enhetsfilerna i Home Assistants mapp `config/esphome` och ESP32-projekten i SMGD Developer Lab ska hållas lika, åt båda håll. Bryggan läser och skriver då i den mappen (se Behörigheter). |
| **Dela hemligheter med SMGD Developer Lab** | Av som standard. Slå på för att en administratör ska kunna hämta värdena för de `!secret` som en synkad enhet använder från Device Builders `secrets.yaml` till projektets hemligheter (lagras krypterat), så att projektet kan byggas i molnet. Kräver synken ovan. Utan den här inställningen lämnar värdena i `secrets.yaml` aldrig Home Assistant. |
| **VCI** | Av som standard. Slå på för att en administratör i SMGD Developer Lab ska kunna ge bryggan VCI-jobb (se avsnittet VCI nedan). Molnet kan inte slå på den. |
| **Serieport för VCI** | Porten som VCI:n sitter på, helst sökvägen under `/dev/serial/by-id`. **Krävs för alla VCI-jobb utom *Leta portar*** – utan den skickar bryggan ingenting till någon port, inte ens när datorn bara har en serieport (den kan vara en Zigbee- eller Z-Wave-sticka). Jobbet *Leta portar* listar portarna (by-id-namnen) så att du kan välja rätt. |
| **Max samtidiga jobb** | 1–8. Det körs aldrig mer än ett jobb per enhet. |
| **Extra nedladdningsvärdar** | Behövs normalt inte. |

Ändringar gäller efter omstart av appen.

## Behörigheter

Appen begär åtkomst till Home Assistants API (`homeassistant_api`). Home Assistant ger då åtkomst
till hela API:t; vad bryggan gör med den bestäms av bryggan själv. Den läser enhetsregistret
(bekräfta firmwareversion efter en uppdatering) och – bara med **LAN Gateway** påslagen och bara för
testsessioner som en administratör godkänt – släpper den igenom anrop från en testad app. De är
skrivskyddade, utom när **Home Assistant via gatewayen** står på `interactive`: då får appen även anropa
de tjänster och entiteter som en administratör uttryckligen godkänt.

Appen har dessutom **läs- och skrivåtkomst till Home Assistants konfigurationsmapp**
(`homeassistant_config`). Home Assistant kan inte dela bara en undermapp, så det gäller hela mappen.
Den behövs bara för **Synk med ESPHome Device Builder**, eftersom Device Builder bara har sina
enhetsfiler där (`config/esphome`) och inget API för andra appar. Med synken avstängd rör bryggan inte
mappen. Med den påslagen läser och skriver bryggan bara enhetsfilerna direkt i `esphome/`, läser de
komponentmappar som enhetsfilerna bygger från, läser namnen i `secrets.yaml` och lägger till en rad där
bara när en administratör ber bryggan flytta ett lösenord som står i klartext. Värden i `secrets.yaml`
läses ut bara för hemligheter som en synkad enhet använder, och bara om **Dela hemligheter med SMGD
Developer Lab** är påslagen.

För VCI (se nedan) begär appen dessutom åtkomst till värdens **serieportar** (`uart`) och till
värdens **D-Bus** (`host_dbus`, för Bluetooth via BlueZ). Home Assistant ger de behörigheterna när
appen installeras, men bryggan öppnar ingen serieport och ansluter inte till D-Bus om inte
inställningen **VCI** är påslagen.

Appen begär **inte**: öppna portar, host-nätverk, privilegierat läge, Supervisor-API eller Docker-socket.

## VCI

Med inställningen **VCI** påslagen kan en administratör i SMGD Developer Lab ge bryggan jobb mot en
Foxwell VCI som sitter i Home Assistant-datorns USB-port (och, för Bluetooth-modulens inställningar,
är inom Bluetooth-räckhåll för den):

| Jobb | Vad bryggan gör |
|---|---|
| Sök | Listar serieportar och Bluetooth-enheter i närheten. |
| Läs info | Läser namn, serienummer, version, tillverkningsdatum, typ och läge över USB och MAC-adressen över Bluetooth. |
| Skriv serienummer | Skriver serienumret över USB, sätter samma Bluetooth-namn och läser tillbaka båda. |
| Skriv MAC | Sätter Bluetooth-modulens adress, ansluter på nytt och läser tillbaka den. |
| Byt läge | BOOT eller APP; läget läses från enheten efteråt. |
| Firmware | Hämtar filen från biblioteket i Developer Lab, kontrollerar SHA-256 och att filen är för enhetens typ, överför den och läser version och läge efteråt. |
| Nyckel | Kontrollerar att enheten har det serienummer administratören angett och skriver nyckelpaketen. |

Så här fungerar det:

- Bryggan gör **hela jobbet lokalt**. Inget skickas paket för paket genom molnet, och det finns
  ingen terminal eller möjlighet att skicka egna kommandon den här vägen.
- Ett jobb räknas som **lyckat** först när enheten kvitterat och värdet lästs tillbaka från den.
  Annars står det *misslyckat* eller *obekräftat* – med det enheten faktiskt svarade.
- Bluetooth ansluts bara till den enhet vars namn är exakt det namn som lästs över USB. Finns flera
  enheter med samma namn i närheten avbryts jobbet.
- Firmwarefiler hämtas bara från API:ts egna värdar (och de extra nedladdningsvärdar du angett),
  hålls i minnet och sparas inte på disk.
- Nyckelpaketen kommer krypterade för just den här bryggan och det jobbet, öppnas i minnet, skrivs
  aldrig i loggen och sparas inte. Vem som beställde skrivningen och för vilken enhet loggas i
  Developer Lab.
- Ett VCI-jobb i taget. Ett jobb som ingen brygga hunnit ta inom tio minuter utförs inte.
- Bryggan öppnar bara **den port som valdes när jobbet började**. Startar enheten om (lägesbyte,
  firmware) väntar bryggan på att samma port kommer tillbaka; den öppnar aldrig en annan port i
  stället. Kommer porten inte tillbaka under samma namn slutar jobbet som *obekräftat*, och i
  resultatet står vilka portnamn som dök upp.
- Resultatet av ett jobb sparas i bryggans datakatalog tills servern tagit emot det. Tappar bryggan
  kontakten med internet under ett jobb görs jobbet ändå klart mot enheten; i Developer Lab står det
  *obekräftat* tills bryggan fått fram sitt resultat, och rättas då till det enheten bekräftade.
- Stoppas eller uppdateras appen medan ett jobb pågår görs jobbet först klart, om det hinner bli klart
  inom fem minuter (längre väntar inte Home Assistant). Bryts bryggan ändå mitt i ett jobb – en längre
  firmwareöverföring, strömavbrott, krasch – rapporterar den vid nästa start att jobbet avbröts och om
  något hann skickas till enheten.

Nyckeln kan inte läsas tillbaka från enheten. En nyckelskrivning där enheten kvitterat varje paket
står därför som **obekräftad** (*alla paket kvitterade, nyckeln kan inte läsas tillbaka*) – aldrig
som lyckad. Det är det mesta enheten kan bekräfta för en nyckel.

## Lagring

Allt ligger i appens egen datakatalog (`/data`) och följer med vid omstart, uppdatering och
säkerhetskopiering av appen:

| Katalog/fil | Innehåll |
|---|---|
| `identities/` | Bryggans nycklar och åtkomstuppgifter (endast läsbara för appen) |
| `devices/<enhet>/` | TV-nyckel och låst värdnyckel för varje parkopplad TV |
| `journal/` | Pågående och avslutade jobb, så att en omstart aldrig upprepar en installation |
| `vci-jobs/` | VCI-jobb vars resultat servern inte tagit emot än (jobbets id, utfall och det enheten svarade – aldrig nyckelpaket) |
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
| `api_url must use https` i loggen | Ange API-adressen med `https://` under Konfiguration. |
| `Invalid option allowed_networks` i loggen | En rad under **Tillåtna nätverk** är inte ett nätverk i CIDR-form (t.ex. `192.168.1.0/24`). Rätta eller ta bort raden. |
| `cannot reach the API; retrying` | Kontrollera internetanslutningen och API-adressen. Bryggan försöker igen av sig själv. |
| `this bridge has been revoked or replaced` | Bryggan har återkallats i SMGD Developer Lab. En ny parkopplingskod visas i loggen. |
| TV:n visas som offline | Kontrollera att TV:n är på, att utvecklarläget är aktivt (det stängs av efter en tid) och att adressen stämmer. |
| "SSH-värdnyckeln har ändrats" | TV:n har fått en ny nyckel (t.ex. efter fabriksåterställning) – eller så svarar något annat på adressen. Granska och godkänn i SMGD Developer Lab. |
| "Adressen ligger utanför de nätverk som är tillåtna" | Lägg till nätverket under **Tillåtna nätverk**, om enheten verkligen ska nås. |

## Vad som inte är verifierat

Provat på riktig hårdvara (2026-11-15, med tidigare versioner av appen i en riktig Home Assistant):
synk med ESPHome Device Builder, flytt av lösenord till `secrets.yaml`, hämtning av hemligheter,
komponentmappar och OTA-uppdatering av en Seeed XIAO ESP32-S3. Avbilden för aarch64 byggs i GitHub
Actions.

**Inte** provat: någon riktig LG-TV, versionsbekräftelse via byggtid mot en riktig Home Assistant och
de ändringar som kommit sedan dess (bland dem allt i 0.1.5).

VCI-delen är bara provad mot en simulerad VCI: varken en riktig serieport, BlueZ/D-Bus i Home
Assistant eller en riktig VCI har använts. Det är inte känt om VCI:n kommer tillbaka under samma
portnamn efter ett lägesbyte, och inte prövat att Home Assistant verkligen väntar den förlängda
stopptiden. Om Home Assistant visar `/dev/serial/by-id` för appen och
om dess säkerhetsprofil släpper igenom D-Bus-anropen till BlueZ är alltså inte bekräftat.

Aktuell status finns i projektets `IMPLEMENTATION_STATUS.md`.
