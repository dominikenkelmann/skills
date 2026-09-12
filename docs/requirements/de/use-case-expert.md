## Was er tut

`use-case-expert` ist ein Requirements Engineer. Er nimmt eine rohe Feature-Idee, eine User Story oder ein vages PRD und formt daraus eine formale Use-Case-Spezifikation nach Cockburn/RUP.

Er schreibt keinen Anwendungscode. Seine Aufgabe ist es, Mehrdeutigkeit zu beseitigen, *bevor* jemand die Codebasis anfasst — indem er unausgesprochene Grenzen aufdeckt, aktive alternierenden Schritte erzwingt, Randfälle in Extension-Branches extrahiert und Systemgarantien mit der Formel `System bestätigt, DASS [...]` absichert.

## Wann einsetzen

| Ausgangslage | Was er tut |
| --- | --- |
| Neues Feature oder neue Idee | Wandelt eine lose User Story oder ein PRD in eine formale, build-fähige `UC-XXX`-Spezifikation um. |
| Bestehende Spezifikation prüfen | Prüft einen Use-Case-Entwurf auf vage Formulierungen, fehlende Fehlerpfade oder nicht testbare Nachbedingungen. |
| Härtung nach einem Vorfall | Überführt einen unbehandelten Produktionsfehler in einen dokumentierten Extension-Branch. |

Aufruf mit `/use-case-expert` oder durch Formulierung deiner Feature-Anforderung.

**Nicht** für die Implementierung einer freigegebenen Spezifikation (→ `use-case-implementer`) oder die Extraktion von Spezifikationen aus Legacy-Code (→ `use-case-reverse-engineer`).

## Voraussetzungen

Keine.

Er schreibt nach:
- `docs/usecases/UC-XXX <Verb Substantiv>.md` — der formale Use Case
- `docs/usecases/supplementary-specification.md` — übergreifende NFRs, UI-Einschränkungen und Geschäftsregeln

## Der Kernstandard

Jeder Use Case folgt vier Strukturregeln:

1. **Vorbedingungen** — überprüfbarer Systemzustand vor Beginn des Ablaufs.
2. **Alternierende Schritte** — striktes Wechselspiel zwischen Primärakteur und System. Kein Schritt ohne Gegenstück.
3. **Vollständige Erweiterungen** — jede Validierung, jede Verzweigungsbedingung erhält eine nummerierte Erweiterung (`3a`, `3b`).
4. **Die THAT-Formel** — Nachbedingungen beschreiben konkrete Zustandsmutationen:
   - ❌ *„Der Benutzer wird aktualisiert."*
   - ✅ *„System bestätigt, DASS `User.email` persistiert ist UND ein Bestätigungstoken versendet wurde."*

## Was danach passiert

Nach Prüfung und Freigabe den Use Case an **`use-case-implementer`** übergeben — er erzeugt die TDD-Testsuite und baut den Vertical Slice gegen die Spezifikation.
