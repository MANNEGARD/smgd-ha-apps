# Ändringslogg

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
