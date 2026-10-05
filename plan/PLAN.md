# PLAN — EBB
> Nur der Planer schreibt. Worker lesen. Status wird von pw.py für UEBERGABE_FAKTEN.md ausgewertet (letzte Spalte).

## Ziel
Pflege des BigTreeTech-EBB-Repositories (CAN-Bus-Toolhead-Boards für Klipper): Hardware-Unterlagen, vorkompilierte Firmware und Klipper-Beispielkonfigurationen bleiben je Board-Version (V1.0, V1.1, V1.2, SB2209, SB2240, RP2040) konsistent und korrekt. Änderungen laufen über Kontrakte mit prüfbarer Abnahme; Pin-Zuordnungen werden ausschließlich gegen die Hardware-Dateien verifiziert.

## Fakten (aus Bestandsaufnahme)
| Fakt | Quelle | Konsequenz |
|---|---|---|

## Annahmen (offen, zu verifizieren)
| Annahme | Prüfung durch | Status |
|---|---|---|

## Aufgaben
| ID | Titel | Worker | Stufe | Dateien (für Parallelprüfung) | Abhängig von | Parallel | Status |
|---|---|---|---|---|---|---|---|
| T-00 | Sicherheit / Secrets | worker-code | S1 | … | – | – | OFFEN |
| T-01 | Bestandsinventur | worker-format | S0 | … | – | ja | OFFEN |

Status: OFFEN · VERGEBEN · BLOCKED · ABNAHME · DONE · VERWORFEN

## Abhängigkeitskette
```
T-00 → T-01 ─┬→ T-02
             └→ T-03 → T-04
```

## Entscheidungen
| Datum | Entscheidung | Begründung | Verworfene Alternative |
|---|---|---|---|

## Eskalationen (S2/S3)
| Datum | Aufgabe | Stufe | Ziel | Abbruchkriterium | Ergebnis |
|---|---|---|---|---|---|

## Risiken
| Risiko | Auswirkung | Gegenmaßnahme |
|---|---|---|
