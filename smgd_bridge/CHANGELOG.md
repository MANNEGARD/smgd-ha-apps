# Ändringslogg

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
