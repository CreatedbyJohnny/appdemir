# AGENTS.md

Briefing für den Coding-Agenten. Lies diese Datei zu Beginn jeder Session, danach `SPEC.md` und `docs/backlog.md`.

## Projekt

<Ein Satz: Was macht die App, für wen?> Fachliche Grundlage ist `SPEC.md` im Repo-Root. Ändere die SPEC.md nie ohne Rückfrage.

## Stack

- Framework: <z. B. Next.js>
- Datenbank: SQLite über Prisma 7 (lokale Datei, kein Server). Installieren mit `npm install prisma@7 @prisma/client@7`, keine Vorabversion.
- UI: Tailwind CSS, Gestaltung siehe Abschnitt „Design“
- Start: `npm run dev`, danach http://localhost:3000

## Design

Die App soll aussehen wie ein gutes Produkt, nicht wie ein Tailwind-Beispiel.

- Leg beim ersten Feature eine Richtung fest (z. B. ruhig und editorial, freundlich und farbig oder technisch und präzise), schreib sie in `docs/decisions.md` und halte sie überall durch.
- Farben: eine getönte Grundfläche statt reinem Weiß und Grau (z. B. warmes Off-White oder Stein), eine kräftige Akzentfarbe für Hauptaktionen und den aktiven Menüpunkt, sonst sparsam. Kein Standard-Indigo, keine Lila-Blau-Verläufe.
- Schrift: eine Schrift mit Charakter (über `next/font` oder Google Fonts), gern eine eigene für Überschriften. Nicht nur die Systemschrift.
- Hierarchie über Größe, Gewicht und Abstand: Seitentitel deutlich größer, Labels klein und gedämpft, Zahlen und Datumswerte mit `tabular-nums`.
- Großzügige Abstände, zusammengehörige Inhalte auf Karten oder Flächen, einheitliche Radien und Schatten.
- Tabellen: angenehme Zeilenhöhe, dezente Trennlinien und Hover, Status als farbige Badges, Zahlen und Aktionen rechtsbündig.
- Formulare: Labels über den Feldern, sichtbarer Fokus, Pflichtfelder markiert, Fehlermeldungen direkt am Feld.
- Leere Listen, Ladezustände und Fehler bekommen eine eigene, freundliche Darstellung.
- Icons aus einer Bibliothek (z. B. lucide), einheitlich. Keine Emojis als Icons.

## Arbeitsweise

- Ein Feature pro Session, Feature-IDs aus `docs/backlog.md` (z. B. F-001).
- Erst einen Plan zeigen, dann bauen, wenn ich zustimme.
- Starte die App selbst und prüfe, dass sie läuft. Lies Fehler im Terminal und behebe sie.
- Schreib für jede Business Rule aus der SPEC.md mindestens einen Test.
- Änderungen am Datenmodell immer als Prisma-Migration (`npx prisma migrate dev`), nie die Datenbankdatei von Hand ändern.
- Erkläre mir in zwei Sätzen, was du geändert hast, auf Deutsch und ohne Fachjargon.

## Backlog (`docs/backlog.md`)

- Ein Feature ist ein Ablauf, den eine Person in der App von Anfang bis Ende durchspielen kann, z. B. „Exemplar ausleihen und zurückgeben“. Datenmodell, Oberfläche, Regeln und Tests gehören in dieses Feature.
- **Keine eigenen Features** für Datenmodell, einzelne Entitäten oder Felder, Rollen und Rechte, Layout, Design oder Projekt-Setup. Das entsteht beim ersten Feature, das es braucht.
- Insgesamt **8 bis 15 Features**, davon **Phase 1 höchstens 5**. Werden es mehr, fass zusammen und frag mich, bevor du neue Features anlegst.
- Jedes Feature nennt die Business Rules (BR) und Entitäten aus der SPEC.md, die es abdeckt.
- Format: Tabelle mit ID (F-001 …), Name, Phase, Status (offen, in Arbeit, fertig), BR/Entitäten.

## Kurzbefehle

- **„Doku“**: Trag Entscheidungen in `docs/decisions.md` ein (Datum, was, warum) und aktualisiere den Status in `docs/backlog.md`.
- **„commit“**: `git add -A`, Commit mit Feature-ID in der Nachricht (z. B. `feat: F-001 Exemplar anlegen`), dann `git push`.

## Nicht tun

- Keine Matrikelnummern oder echten Personendaten ins Repo.
- Keine API-Keys in Dateien schreiben.
- Nicht mehrere Features auf einmal bauen.
