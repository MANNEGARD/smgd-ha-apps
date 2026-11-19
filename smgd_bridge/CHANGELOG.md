# Ändringslogg

## 0.1.14

- **BLE exakt som webbläsaren:** bara FFF1 får notiser påslagna. Med FFF3 påslagen (0.1.11–0.1.13) skickade modulen
  ett statusmeddelande där och inget på datakanalen. Övriga karakteristiker listas fortfarande i felsökningen.

## 0.1.13

- **BLE som i webbläsaren:** inget skickas före protokollets första fråga (AT-stängningen före jobbet är borttagen).
- Felsökningsuppgifterna visar de första svaren från modulen (vilken kanal, längd och början).

## 0.1.12

- **BLE: färska GATT-uppgifter.** BlueZ sparar enhetens tjänster från tidigare anslutningar; när de är inaktuella
  hamnar skrivningar och notiser på fel ställe och inget kommer tillbaka. En enhet som inte är parkopplad tas därför
  bort ur BlueZ en gång före anslutningen och hittas igen, så att tjänsterna läses på nytt – som i webbläsaren.

## 0.1.11

- **BLE:** bryggan lyssnar på alla karakteristiker i modulens FFF0-tjänst som kan skicka notiser, inte bara FFF1,
  och listar dem med sina egenskaper i jobbets felsökningsuppgifter.

## 0.1.10

- **Felsökning av BLE:** när varken VCI:n eller modulen svarar redovisar jobbet vad som hände på länken: antal
  skrivningar och notiser, om notiserna är påslagna, enhetens GATT-tjänster och anslutningsläge (inga data).

## 0.1.9

- **BLE:** bryggan stänger Bluetooth-modulens AT-motor innan protokollet körs (en motor som lämnats öppen släpper
  inte igenom något till VCI:n), och efter AT-kommandon mitt i ett jobb.
- **Tydligare fel över Bluetooth:** svarar inte VCI:n frågar bryggan modulen själv och säger om länken fungerar men
  VCI:n bakom är tyst (troligen ström från bilen behövs) eller om inget alls kommer tillbaka.
- BLE-skrivningar görs med eller utan svar efter vad modulens karakteristik stöder, som i webbläsaren.
- SPP-felet visar också orsaken till det första försöket.

## 0.1.8

- **SPP utan PIN:** bryggan ansluter först utan parkoppling (de flesta VCI:er tillåter det). Bara om VCI:n vägrar
  parkopplas den, med `vci_spp_pin` (standard **0000**, var 1234) eller utan PIN om modulen inte frågar efter någon.

## 0.1.6

- **VCI över Bluetooth utan USB.** Ett VCI-jobb kan nu köras helt över Bluetooth – med **BLE** (Feasycom-modulens
  datakanal) eller **SPP** (klassisk Bluetooth, RFCOMM) – mot den enhet du anger i Developer Lab (namn, eller namn
  eller adress för SPP). Som i webbläsaren går hela protokollet då över Bluetooth-länken; USB behövs inte och
  `vci_serial_port` behöver inte vara satt. USB fungerar som förut.
- **SPP:** bryggan parkopplar enheten första gången med sin egen parkopplingsagent. Ny inställning
  `vci_spp_pin` (PIN-kod vid parkoppling, standard 0000 för Feasycom-moduler).
- Bryggan rapporterar vilka anslutningar den klarar, så att Developer Lab bara erbjuder dem.
- Inte provat mot en riktig VCI över Bluetooth: provat mot den simulerade enheten.

## 0.1.5

Säkerhetsuppdatering efter en genomgång av hela plattformen.

- Parkoppling: bryggan signerar sin parkopplingsbegäran med sin egen nyckel. Servern tar inte
  längre emot en begäran utan signatur, så nya parkopplingar kräver den här versionen. En brygga
  som redan är parkopplad påverkas inte.
- LAN Gateway mot Home Assistant: filtret för godkända entiteter och tjänster går inte längre att
  kringgå med återanvända meddelande-id, annan teckenkodning eller för stora svar. Det som skickas
  vidare är alltid det bryggan själv kontrollerat.
- LAN Gateway: Home Assistants interna nätverk (172.30.32.0/23) nås inte via gatewayen om det inte
  uttryckligen står i `allowed_networks`.
- Synk med ESPHome Device Builder: lösenord i klartext upptäcks nu även i block-, klammer- och
  citatform samt på egen rad. Komponentmappar får samma kontroll som enhetsfiler: en `secrets.yaml`
  eller en YAML-fil med lösenord i klartext laddas aldrig upp.
- Hämtning av hemligheter (`esphome_builder_share_secrets`): för en fil som Developer Lab själv har
  skrivit lämnas bara de hemligheter ut som filen hänvisade till innan dess.
- Anslutningen till relay förnyas löpande; en återkallad brygga kopplas ifrån.
- **Nya inställningar, av som standard:** `gateway_enabled` (LAN Gateway) och `gateway_home_assistant`
  (off / read_only / interactive). Molnet kan inte längre slå på åtkomst till hemnätet eller till Home
  Assistant; det avgörs här. **Använder du LAN Gateway i Test Lab måste du slå på den efter uppdateringen.**
- **VCI Workshop (ny inställning `vci_enabled`, av som standard):** bryggan kan utföra VCI-jobb lokalt
  mot en Foxwell VCI i datorns USB-port och via Bluetooth – läs info, skriv serienummer/namn/MAC,
  byt läge, firmware och nyckel. Ingen terminal den vägen. Appen begär därför åtkomst till
  serieportar (`uart`) och värdens D-Bus (`host_dbus`); de används inte om inställningen är av.
  Provat mot en simulerad VCI, inte mot riktig hårdvara.
- VCI: resultatet av ett jobb sparas i datakatalogen tills servern tagit emot det (även över avbrott
  och omstart); ett pågående jobb får upp till fem minuter att bli klart innan appen stoppas (`timeout: 300`, det
  mesta Home Assistant godtar); bryggan öppnar bara
  den serieport som valdes när jobbet började och gissar inte när det finns flera portar – ange då
  `vci_serial_port`; en nyckelskrivning står som obekräftad eftersom nyckeln inte kan läsas tillbaka.

## 0.1.4

- OTA: enheten hittas i Home Assistant även när namnen skiljer sig i skiftläge, bindestreck och
  andra skiljetecken ("Sovrum Temp/Humid" och "sovrum-temp-humid"). Finns flera poster med samma
  namn räknas bara de som ser ut som ESPHome-enheter. När ingen hittas står det i resultatet vad
  Home Assistant kallar sina ESPHome-enheter.

- webOS: svar från TV:ns systemanrop läses tåligare (flera rader, inledande meddelanden) och begärs
  en gång till efter en kort paus. Går svaret ändå inte att läsa står det i felet vad TV:n svarade.

## 0.1.3

- Synk med ESPHome Device Builder: lokala komponentmappar som en enhetsfil bygger från
  (`external_components` med lokal källa) följer nu med till projektet, så att enheten kan byggas i
  molnet. Bara mappar som en enhetsfil pekar ut läses; länkar följs aldrig, och filer som ser ut att
  innehålla privata nycklar laddas inte upp. Bryggan skriver aldrig i mapparna.

## 0.1.2

- OTA: firmware utan egen versionsmärkning (t.ex. en fil från ESPHome Device Builder) bekräftas nu
  via Home Assistant genom ESPHome-version och byggtid. Bryggan väntar upp till 45 sekunder på att
  Home Assistant ska visa den nya firmwaren efter omstarten.
- Tydligare besked om varför en version inte kunde läsas av.

## 0.1.1

- Synk med ESPHome Device Builder: på begäran från en administratör i SMGD Developer Lab kan
  bryggan flytta lösenord och nycklar som står i klartext i en enhetsfil till Device Builders
  `secrets.yaml` och lämna `!secret` på raden. Värdena lämnar aldrig bryggan; befintliga rader i
  `secrets.yaml` ändras inte.
- Avbilden för aarch64 byggs utan emulerad `npm`.

## 0.1.0

Första versionen.

- Parkoppling med engångskod, automatisk förnyelse av åtkomst, återkallning från SMGD Developer Lab.
- LG webOS: parkoppling i utvecklarläge, installation, start, stängning, avinstallation, applista
  och logginsamling. Värdnyckeln låses vid parkoppling; en ändrad nyckel stoppar allt tills en
  administratör godkänt den.
- ESPHome: OTA-uppdatering med kontroll av att enheten kommer tillbaka; versionen bekräftas via
  Home Assistant när det går.
- LAN Gateway för testsessioner mot godkända lokala tjänster, med skrivskyddat läge för Home Assistant.
- Hälsokontroll, statusfil och inställning för att stänga av ESP OTA.

Inte verifierat i den här versionen: körning på en riktig Home Assistant OS-installation, mot en
riktig LG-TV eller en riktig ESP32. Se projektets `IMPLEMENTATION_STATUS.md`.
