# AGENTS.md – Arbeitsregeln (Kern, werkzeugneutral)
> Gilt für jeden KI-Agenten und jede Sitzung in diesem Repository. Enthält keine Werkzeug- oder Modellnamen;
> die Zuordnung zu einem konkreten Werkzeug liefert dessen Adapter (Claude Code: Plugin `pw`).
> Änderungen nur durch den Nutzer oder mit seiner ausdrücklichen Freigabe (Commit `regeln: …`).

## 1 Rollen
| Rolle | Darf | Darf nicht |
|---|---|---|
| Nutzer | freigeben, priorisieren, entscheiden, Regeln ändern | – |
| Planer (Hauptsitzung) | lesen, suchen, Tests/Build ausführen, in `plan/` schreiben, nach Freigabe committen | Produktivcode schreiben, ohne Freigabe pushen |
| Worker (Umsetzung) | genau einen freigegebenen Kontrakt umsetzen, nur in dessen Pfaden | committen, Abhängigkeiten hinzufügen, nebenbei umbauen, raten |
| Worker (mechanisch) | Daten umwandeln, zählen, Tabellen erzeugen | Rohdaten verändern, Inhalte deuten |

## 2 Ablauf je Aufgabe
```
Planer schreibt Kontrakt → Nutzer gibt frei → Worker setzt um + Bericht
→ Planer führt Abnahme SELBST aus → bestanden: Commit · nicht bestanden: Nachkontrakt T-XXb
```
- Kontrakt: `plan/tasks/T-XX.md` nach Vorlage. Ohne maschinell prüfbare Abnahme ist eine Aufgabe nicht fertig geplant.
- Schnitt: eine Aufgabe = ein Modul, eine Route, eine Komponente oder eine Migration; höchstens etwa fünf Dateien.
- Parallel nur mit disjunkten Dateilisten.
- Der Planer flickt nicht selbst. Fehlschlag → Nachkontrakt.
- Ein Kontrakt = ein Commit (`T-XX: was (warum)`, deutsch).
- Unklarheit beim Worker → `STATUS: BLOCKED` mit einer konkreten Frage.

## 3 Freigaben nach Risiko
| Stufe | Beispiele | Verfahren |
|---|---|---|
| niedrig | lesen, suchen, Tests, `git status/diff/log`, Schreiben in `plan/` | ohne Rückfrage |
| mittel | Worker-Änderungen innerhalb eines Kontrakts | einmal je Kontrakt durch den Nutzer |
| hoch | Commit, Push, Merge, Abhängigkeiten, Regel-/Konfigurationsdateien, Modell-Eskalation auf die Spitzenstufe | jedes Mal einzeln |

## 4 Modellstufen (abstrakt)
| Stufe | Einsatz |
|---|---|
| mechanisch | Umwandeln, Zählen, Import, Inventur |
| Standard | Code, Logik (Worker) |
| stark | Planer (Zerlegung, Kontrakte, Abnahme), Neuschnitt nach zweimaligem Scheitern, Architektur, Audit |
| Spitze | Eskalation, nur mit Freigabe, Ziel und Abbruchkriterium |
Grundsatz: erst besser schneiden, dann stärker rechnen.

## 5 Git
- `main` ist stabil. Arbeit auf `planer-worker` bzw. Feature-Branches. Merge nach `main` nur mit Freigabe.
- Remote: privat. Kein Force-Push, kein Umgehen von Git-Hooks.

## 6 Ablage
| Ordner | Inhalt | Regel |
|---|---|---|
| `src/`, `tests/` | Code, Tests | nur Worker |
| `daten/roh/` | Originaldaten | unveränderlich; Herkunft in `QUELLEN.md`; nicht in Git (ignoriert) – Sicherung liegt beim Nutzer |
| `daten/verarbeitet/` | erzeugte Daten | reproduzierbar über `werkzeuge/` |
| `werkzeuge/` | Hilfsskripte | nur Worker |
| `docs/` | Dokumentation | per Kontrakt |
| `plan/` | Plan, Kontrakte, Berichte, Übergabe, Kennzahlen, Audits | Planer; Berichte: Worker |
Dateinamen: Kleinbuchstaben, Bindestriche, keine Umlaute, Datum `JJJJ-MM-TT`.

## 6a Datenhaltung
Jede Tatsache hat genau **eine maßgebliche Quelle** (Single Source of Truth). Wo dieselbe Information an mehreren Stellen liegt, gilt:
- Der Planer benennt im Kontrakt, welche Stelle maßgeblich ist und welche abgeleitet. Ist das nicht entscheidbar: `STATUS: BLOCKED`, der Nutzer entscheidet.
- Abgeleitete Kopien (Cache, statischer Rückfall, Export, Bericht) müssen regenerierbar und als abgeleitet gekennzeichnet sein. Eine Kopie, die zur eigenständigen Quelle geworden ist, ist ein Befund für den Nutzer.
- Kein Worker legt eine zweite Kopie bestehender Daten an – auch nicht vorläufig oder zum Testen.
- Kein Worker löst bestehende Redundanz eigenmächtig auf: melden, nicht bereinigen. Es gilt weiter: nichts löschen, was Geld gekostet hat.
- Neue Datenstrukturen werden normalisiert angelegt (3NF als Richtschnur). Bewusste Denormalisierung nur mit Begründung im Kontrakt und Freigabe des Nutzers.
- Ausnahme Testdaten: Erfundene oder per Skript erzeugte Daten unter `tests/` sind keine Kopie im Sinne dieser Regel. Ein Auszug echter Daten ist eine Kopie – auch unter `tests/`.

## 7 Zugangsdaten
Zugangsdaten stehen ausschließlich in `.env` (nicht versioniert). Im Code nur Verweise auf Umgebungsvariablen.
Ein Wert, der je in der Git-Historie stand, wird beim Anbieter erneuert – Löschen genügt nicht.

## 8 Sitzungen
- Start: Übergabe lesen (`plan/UEBERGABE.md`, `plan/UEBERGABE_FAKTEN.md`), Abweichungen zum Repo melden.
- Ende: Übergabe aktualisieren, committen, Hash prüfen. Mehrere Rechner: pushen, Sperre freigeben.

## 9 Audit
Regelmäßig und bei Werkzeug-/Modellwechsel: veraltete Teile finden, Neuerungen prüfen, Kennzahlen bewerten,
**nicht genutzte Bausteine entfernen**. Audits ändern nichts selbst – sie erzeugen Befunde und Kontrakte.

## 10 Sprache
Code, Befehle, Dateinamen, Bezeichner unverändert. Erklärungen, Rückfragen, Berichte, Commit-Nachrichten: Deutsch.
