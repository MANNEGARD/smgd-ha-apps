# Ändringslogg

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
