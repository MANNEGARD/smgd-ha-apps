# SMGD Bridge

Lokal agent för **SMGD Developer Lab**. Bryggan körs i ditt hemnätverk och utför det som
molnet inte kan nå: installerar appar på LG webOS-TV, uppdaterar ESPHome-enheter över
nätverket och ger testsessioner åtkomst till uttryckligen godkända lokala tjänster.

- Gör bara **utgående** anslutningar. Öppnar inga portar, behöver varken host-nätverk eller
  utökade rättigheter.
- TV-nycklar och bryggans identitet lagras bara i appens egen datakatalog.
- Kompilerar aldrig något – byggen sker i molnet.

Installation, parkoppling och felsökning: se fliken **Dokumentation** (`DOCS.md`).
