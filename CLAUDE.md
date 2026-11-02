# Escape-Room „Das Gutachten“ – Hinweise für die Entwicklung

Escape-Room für Evangelische Religion, Jahrgangsstufe 11 (Einführungsphase, E1): Religionsbegriffe, Funktionen von Religion
nach Stoodt, Fallbeispiel Pastafari. Story: Die Sachverständige Prof. Dr. Marlene Hagedorn ist erkrankt, ihr Gutachten zum
Rechtsstreit der „Kirche des Fliegenden Spaghettimonsters“ (Nudelmesse-Schilder in Templin) liegt im Tresor – die
Schüler:innen sind ihre wissenschaftliche Hilfskraft und vollziehen ihre Prüfschritte in sechs Aktenordnern nach. Sechs
Stationen (Aktenordner; Bibliotheksraum → Rätselraum, kein Weg zurück), je ein Buchstabe für das Codewort STOODT, am Ende
der Arena-Tunnel.
Oberfläche deutsch, Schüler:innen werden geduzt, echte Umlaute und typografische Anführungszeichen („…“).

## Aufbau

- `index.html` – der ganze Raum in einer Datei (CSS, HTML-Gerüst, ein klassisches `<script>`), keine externen Bibliotheken
  oder CDNs. Reihenfolge im Skript: Konfiguration → Zustand → PDF-Writer → Lösungserläuterungen/Labels → Überblick-PDF →
  Datensatz/Tunnel → Bildschirme (Start, Einführung, Bibliotheks- und Rätselräume, Finale) → Zwischenstand → `renderStart()`.
- Bildschirme: `start` → (`resume`) → `intro` → `lib1` → `room1` → … → `final`. Keine History-Einträge, kein Zurück vom
  Rätselraum in die Bibliothek. Bibliothekstexte, Rätsel und Lösungserläuterungen sind von der Lehrkraft freigegeben und
  wörtlich übernommen – Inhalte nur dort ändern.
- Videos: YouTube erst nach Klick (youtube-nocookie, Lader im `<head>`), daneben ein Link, der das Video direkt auf YouTube öffnet (neuer Tab).
  Einführung („Anleitung: Escape-Room meistern“) und Arena („Anleitung: Das Feiditor-Feibeitsblatt als Endgegner“) nutzen die
  Anleitungsvideos der Reihe. Nie „von deiner Lehrkraft geprüft“ schreiben – den öffentlichen Raum setzen auch andere
  Lehrkräfte ein.
- `fonts/` – Schriften als woff2 (Lizenztexte in `fonts/LIZENZEN.txt`).
- `impressum.html` – Impressum & Datenschutz. Anpassen, wenn sich Datenflüsse ändern.
- `CNAME` – `escape-room-e1rel-stoodt.janrickmer.de` (GitHub Pages, Branch `main`).

## Endgegner: das Feiditor-Feibeitsblatt

Der Raum gehört zur Reihe janrickmer/escape-room-* und arbeitet wie der Escape-Room „FastFashion“ (Jg. 10): **kein
eingebetteter Feiditor, keine Abgabe bei Moodle und keine Auswertung im „Check des Feind(t)es“** – also auch kein
`REFLECT_TASKS`, `RED_FIELDS`, `ROOM_CONTEXT`, `%ESCAPEDATA`. Der Endgegner ist das **Feiditor-Feibeitsblatt** (Repository
janrickmer/Feibeitsblatt): Der Arena-Tunnel öffnet es im selben Tab und übergibt den Datensatz im Link. Der
Bestätigungscode steht nur in der Überblick-PDF (und im Datensatz).

## Schnittstelle v1 zum Feibeitsblatt

- Datensatz `feibeitsblatt-escape`: Hülle `{kind:"feibeitsblatt-escape", v:1, d, s}`; `d` = JSON-String der Daten,
  `s` = Prüfsumme `fbEscapeHash('FbEscape|v1|' + d)` (64 Bit als 16 Hex, Testvektor `fbEscapeHash('FbEscape|v1|{}')` =
  `0bdda452e8d9ff95`). Die Prüfsumme ist nur ein Indiz gegen versehentliche Änderungen, kein Schutz.
- Felder von `d` in dieser Reihenfolge: `code`, `room` {id, name, url}, `person` {vorname, nachname, geburtsdatum TT.MM.JJJJ,
  klasse}, `confirm`, `word`, `completedAt` (ISO), `stations` [{label, attempts, errors [{item, wrong?, right?, note?}]}],
  `notes` [{label, text}], `resumes` [{place, savedAt, loadedAt}]. Die Fehler des ersten Versuchs werden aus den
  gespeicherten Fehlerobjekten (`kind`, `item`, `wrongAnswer`, `correctAnswer`, `explanation`) in Textform gebracht
  (`errorToRecord`), mit demselben Wortlaut wie in der Überblick-PDF. Grenzen wie in der CLAUDE.md des Feibeitsblatts
  (Hülle ≤ 96 KB u. a.).
- Übergabe: Tunnel → `location.assign(FEIBEITSBLATT_URL + '#escape=' + base64url(UTF-8(Hülle)))` (`buildEscapeRecord`,
  `b64url`, `enterTunnel`); Überblick-PDF → eingebettete Datei `feibeitsblatt-escape.json` (`/Type /EmbeddedFile`,
  Kennzeichen `/FbEscape 1`, unkomprimiert, im `/EmbeddedFiles`-Namensbaum und in `/AF`).
- Änderungen an der Schnittstelle nur in beiden Repositorys gemeinsam (neue Version `v`).

## Konstanten

- `FEIBEITSBLATT_URL = 'https://feibeitsblatt.janrickmer.de/'`, `ESCAPE_ROOM = {id:'religion-e1-gutachten-stoodt-pastafari', name:'Das Gutachten', url:'https://escape-room-e1rel-stoodt.janrickmer.de/'}`.
  Legt die Lehrkraft das Escape-Blatt im Feibeitsblatt an, trägt sie dort genau diese Kennung (`id`) und diesen Namen ein.
- **`FEIBEITSBLATT_CODE`** – Code des Escape-Blattes im Feibeitsblatt (6 Zeichen ohne Bindestrich). Zurzeit **leer**: Das
  Finale ist gesperrt, bis die Lehrkraft das Escape-Blatt anlegt und den Code hier einträgt. Legt sie das Blatt neu an (z. B.
  nach dem Löschen oder weil es mit fremden Abgaben gefüllt wurde – der Code ist öffentlich), hier den neuen Code eintragen.
  Solange er leer ist, ist der Tunnel gesperrt und die Überblick-PDF trägt `code: ""`; das Feibeitsblatt nimmt eine solche PDF
  nie an. Deshalb raten Sperrhinweis, Pause-Karte, der Satz über der Speicherleiste und der Hinweis in der PDF im gesperrten
  Zustand zur Zwischenstand-Datei des Raums (Knopf direkt unter dem Sperrhinweis), nicht zur Überblick-PDF; die Ladeseite
  „Weiterspielen“ nennt die Überblick-PDF dann nicht, und eine dort hochgeladene PDF bekommt die Meldung, dass hier die
  Zwischenstand-Datei (.txt) gebraucht wird.
- Testhaken nur für automatische Tests: Ist vor dem Laden `window.__FF_TEST__ = {feibeitsblattUrl, code}` gesetzt (Playwright
  `addInitScript`), gelten diese Werte. Produktiv nie setzen.
- Labels „Ordner I · Der Streitfall und die Grundfrage (Religionsbegriffe)“ (`ROOM_LABELS`, `SOLUTION_NOTES`) nie ändern –
  die Aufträge an die KI im Feibeitsblatt verweisen wörtlich darauf.
- `SAVE_FORMAT = 2`: Zwischenstand-Dateien tragen seit dem Umbau die Klasse; Dateien der Fassung 1 (ohne Klasse) werden mit
  einer eigenen Meldung abgelehnt.

## Regeln

- Jeder Versuch zählt; die Fehler des ersten Versuchs stehen in Textform (`item`, `wrong`, `right`, `note`) in Überblick-PDF
  und Datensatz.
- Eine geladene Zwischenstand-Datei wird vor dem Übernehmen geprüft (`saveCheckData`: Startregeln samt Klasse, genau sechs
  Stationen, Fortschritt passend zum Bildschirm, Bestätigungscode neu berechnet). Die Prüfsummen von Zwischenstand und
  Datensatz stehen im öffentlichen Code – sie schützen vor Beschädigung, beweisen aber nicht, dass der Raum gespielt wurde.
- Bekannte Grenze (ohne Speicherung im Browser nicht zu verhindern): Wer vor einem Rätselraum speichert, den Raum mit Fehlern
  versucht und dann die Datei lädt, startet den Raum neu – die Fehler dieses Versuchs fehlen in PDF und Datensatz; sichtbar
  bleibt nur der Vermerk „Weitergespielt ab …“.
- Personendaten und Eingaben nur per `textContent` oder `escapeHtml` ins DOM. Kein `alert`, Meldungen stehen im Dokument.
  Namen und Klasse nur mit Zeichen, die die Überblick-PDF (WinAnsi) darstellen kann (`pdfPrintable`).
- Kein Server, keine Cookies, kein localStorage, kein `fetch`. YouTube erst nach Klick. Überblick-PDF und Zwischenstand nur
  als Download (`application/octet-stream`), nie in einem neuen Tab. Nach dem Start fragt `beforeunload` vor dem
  Neuladen/Schließen nach (nicht beim Gang durch den Tunnel, `leaving`). Der Link durch den Tunnel bleibt im
  Browser-Verlauf – das sagen `#gateHint` und `impressum.html`.
- „Copyright Jan-Rickmer Feindt“ auf jedem Bildschirm (Footer) und auf jeder PDF-Seite (Fußzeile).
- Automatische Tests (Playwright) liegen nicht im Repository. Live für Schüler:innen ist Branch `main`: vor jedem Push den
  ganzen Durchlauf testen und eine echte Überblick-PDF im Feibeitsblatt hochladen.
