# Escape-Room – Hinweise für die Entwicklung

Dieser Escape-Room gehört zu einer Reihe (janrickmer/escape-room-*), deren Ergebnisse „Check des Feind(t)es“
auswertet (check.janrickmer.de, Repository janrickmer/check-des-feind-t-es; die vollständige Checkliste steht
dort in `CLAUDE.md`). Die Vorgaben gelten für diesen und für jeden neuen Escape-Room, der aus diesem entsteht:

1. **Steckbrief `ROOM_CONTEXT`** (vor `REFLECT_TASKS`): `id`, `fach`, `stufe` (ausdrückliche Jahrgangsstufe –
   im Zweifel bei der Lehrkraft nachfragen), `thema`, `raum` (Name, wie ihn die Schüler:innen sehen) und
   `inhalte` (5–8 im Escape-Room erarbeitete Inhalte, nur aus den Räumen und Lösungserläuterungen dieses
   Escape-Rooms, gegen die Datei auf Faktentreue geprüft und von der Lehrkraft freigegeben).
2. **Weitergabe:** `openFeiditor()` übergibt `ctx: ROOM_CONTEXT` in `window.__ESCAPE_DATA__`; der Feiditor
   speichert ihn verschlüsselt in der PDF. Die Überblick-PDF trägt ihn in `%ESCAPEDATA` (Feld `c`). So erhält
   die KI im Check Jahrgangsstufe und Erwartungshorizont.
3. **Eingebetteter Feiditor** (`<script type="text/template" id="feiditor-src">`): die gemeinsame
   Feiditor-Engine unverändert, byte-identisch mit janrickmer/feiditor. Engine-Änderungen immer in alle elf
   Feiditoren einspielen (normaler Feiditor, Lerntagebuch, neun Escape-Rooms).
4. **Neuer Escape-Room:** im Check zusätzlich Einträge in `ESCAPE_ROOMS`, `KNOWN_MAGIC` und `ROOM_MAGIC`;
   Überblick-PDF mit denselben Seitentiteln und Labels wie hier.
5. **Live für Schüler:innen** (Branch `main`): vor jedem Push mit echten, aus diesem Escape-Room erzeugten
   Dateien im Check testen.
