# SMGD Bridge

Lokal agent för **SMGD Developer Lab**. Bryggan körs i ditt hemnätverk och utför det som
molnet inte kan nå: installerar appar på LG webOS-TV, uppdaterar ESPHome-enheter över
nätverket och ger testsessioner åtkomst till uttryckligen godkända lokala tjänster.

- Gör bara **utgående** anslutningar. Öppnar inga portar och behöver varken host-nätverk,
  privilegierat läge eller Supervisor-API.
- Begär åtkomst till Home Assistants API och, för den valfria synken med ESPHome Device Builder,
  till konfigurationsmappen; för VCI (valfritt) till serieportar och D-Bus. Vad varje behörighet
  används till står under **Behörigheter** i dokumentationen.
- TV-nycklar och bryggans identitet lagras bara i appens egen datakatalog.
- Kompilerar aldrig något – byggen sker i molnet.

Installation, parkoppling och felsökning: se fliken **Dokumentation** (`DOCS.md`).
