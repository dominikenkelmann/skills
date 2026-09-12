## Was er tut

`use-case-reverse-engineer` liest bestehenden Quellcode — Controller, Modelle, Endpunkte, CLI-Handler — und rekonstruiert daraus den formalen Use Case, der das tatsächliche Laufzeitverhalten beschreibt.

Er verändert keinen Code. Er verfolgt Kontrollfluss, Datenbankmutationen, externe Aufrufe und Fehlerverzweigungen, um den impliziten Kontrakt freizulegen, der in der Implementierung verborgen liegt.

## Wann einsetzen

| Ausgangslage | Was er tut |
| --- | --- |
| Legacy-Refactoring | Extrahiert eine Ground-Truth-Spezifikation, bevor Legacy-Code abgebaut wird. |
| Vibe-Code aufräumen | Gewinnt Anforderungen aus Prototypen oder schnell generiertem KI-Code zurück. |
| Fehlende Dokumentation | Dokumentiert undokumentierte Endpunkte oder Hintergrundprozesse. |

Aufruf mit `/use-case-reverse-engineer <pfad-zum-code>`.

**Nicht** für das Verfeinern oder Verfassen neuer Spezifikationen (→ `use-case-expert`) oder das Bauen von Code gegen eine bestehende Spezifikation (→ `use-case-implementer`).

## Voraussetzungen

Lesbare Quellcodedateien oder -verzeichnisse im Projekt.

Er schreibt:
- `docs/usecases/UC-XXX <Verb Substantiv>.md` — extrahierter Use-Case-Entwurf
- Eine Liste unbehandelter Randfälle oder toter Verzweigungen, die bei der Analyse entdeckt wurden

## Wie er Spezifikationen extrahiert

Der Skill bildet Code-Konstrukte direkt auf Cockburn-Use-Case-Elemente ab:

| Code-Konstrukt | Extrahiertes Element |
| --- | --- |
| Methodensignatur / Route / HTTP-Methode | **Trigger & Primärakteur** |
| Auth-Guards, Eingabevalidierung, Null-Checks | **Vorbedingungen & Schrittvalidierungen** |
| Erfolgreicher Ausführungspfad & DB-Commit | **Main Flow & Erfolgsgarantien (`THAT`)** |
| `catch`, `try/except`, `if (err) return …` | **Extension-Branches (`3a`, `4a`, …)** |
| Unbehandelte Exceptions | **Fehlende Extension-Kandidaten** |

## Was danach passiert

Den Entwurf an **`use-case-expert`** übergeben, um das Vokabular zu normalisieren und aktualisierte Geschäftsregeln einzuarbeiten. Danach an **`use-case-implementer`** für eine saubere Neuimplementierung oder Regressionstests weiterreichen.
