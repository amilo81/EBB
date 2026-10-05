# T-XX — <Titel>

| Feld | Inhalt |
|---|---|
| Worker | worker-code / worker-format |
| Stufe | S0 / S1 (Eskalation nur nach Leiter) |
| Ziel | <was danach funktioniert, 1 Satz> |
| Dateien | <pfad/datei.py>, <ordner/**> |
| Nicht anfassen | <tabu-Pfade> |
| Kontext | <Datei:Zeile, die der Worker lesen soll, max. 5> |
| Verboten | keine neuen Abhängigkeiten, keine Umbauten nebenbei |
| Maßgebliche Quelle | <Pflicht, sobald die Aufgabe Daten schreibt oder Datenstrukturen anlegt: welche Stelle ist maßgeblich, welche abgeleitet> |
| Abhängig von | <T-IDs, müssen DONE sein> |

> Zeile „Dateien": kommagetrennt, Platzhalter `*` und `/**` erlaubt. Nur diese Pfade schaltet `/pw:freigabe` für Worker frei.

## Vorgaben
1. <konkreter Schritt / Randbedingung>

## Abnahme (maschinell prüfbar)
> Bash-Syntax. Unter Windows führt `pw.py` die Befehle in Git Bash aus.
```bash
<Befehl>        # erwartet: …
```

## Rückgabe
Bericht `plan/reports/T-XX.md` nach Vorlage.

## Standardpunkt Datenhaltung (Pflicht, wenn die Aufgabe Daten schreibt oder Datenstrukturen anlegt)
Der Bericht nennt je betroffenem Datenfeld die maßgebliche Quelle und, falls die Aufgabe eine Kopie erzeugt, warum sie nötig ist und wie sie regeneriert wird. Ohne diese Angabe gilt die Aufgabe als **nicht abgenommen**.
