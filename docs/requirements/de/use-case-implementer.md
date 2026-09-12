## Was er tut

`use-case-implementer` verwandelt einen freigegebenen Use Case (`UC-XXX`) in einen vollständig getesteten Vertical Slice in Code. Er arbeitet in zwei strikten Phasen: zuerst die Testsuite (rot), dann der Produktionscode (grün).

Er weigert sich, Produktionscode zu schreiben, bevor automatisierte Tests existieren, die jeden Main-Flow-Schritt und jeden deklarierten Extension-Branch abdecken. Sobald die Tests stehen, implementiert er den minimalen Code, um sie grün zu machen — und verifiziert, dass jede Nachbedingungsinvariante (`System bestätigt, DASS [...]`) erfüllt ist.

## Wann einsetzen

| Ausgangslage | Was er tut |
| --- | --- |
| Bauen nach Spezifikation | Nimmt einen abgeschlossenen `UC-XXX` und baut den vollständigen Vertical Slice — Domäne, Service, UI, Tests. |
| Testgerüst erzeugen | Erstellt umfassende E2E- oder Unit-Tests aus einem Use Case, ohne Produktionscode anzufassen. |
| Regressionshärtung | Läuft gegen einen aktualisierten Use Case mit neuen Erweiterungen — erzeugt zuerst fehlschlagende Tests. |

Aufruf mit `/use-case-implementer <pfad-zum-use-case>` oder ID es Use Case (UC-123).

**Nicht** für das Verfassen oder Verfeinern der Spezifikation selbst (→ `use-case-expert`) oder die Extraktion von Spezifikationen aus Legacy-Code (→ `use-case-reverse-engineer`).

## Voraussetzungen

Eine Use-Case-Spezifikationatei (`docs/usecases/UC-XXX <Verb Substantiv>.md`) nach dem `use-case-expert`-Standard.

Er schreibt:
- Automatisierte Tests (`tests/`, `*.spec.ts`, `*_test.py`)
- Anwendungscode über den gesamten Vertical Slice (Domäne, Anwendung, Infrastruktur, UI)

## Wie er vorgeht

```
[Freigegebene UC-XXX.md]
        │
        ▼
Phase 1: Testsuite erzeugen (Rot)
├── Happy-Path-Test (Main Flow Schritte 1…N)
└── Branch-Tests (Extensions 3a, 3b, 4a…)
        │
        ▼
Phase 2: Vertical Slice implementieren (Grün)
├── Domänenmodelle & Zustandsübergänge
├── Service-Schicht & externe Adapter
└── UI-Elemente & Validierungshandler
        │
        ▼
[Alle Tests grün · Nachbedingungen verifiziert]
```

## Was danach passiert

Code-Review oder Linter laufen lassen, dann committen. Die Use-Case-Datei bleibt als lebende Dokumentation im Repository solange der umsetzende Code besteht — jeder Test verweist auf einen nummerierten Schritt oder eine Erweiterung.
