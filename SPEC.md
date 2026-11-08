# SPEC.md · Arztpraxis Dr. Demir

Quelle: Meeting 1 (N1–N10) und Meeting 2 (N11–N20) mit Dr. Demir, Praxis mit drei Ärztinnen (Dr. Demir, Dr. Schäfer, Dr. Yilmaz) und mehreren MFAs. Zu jeder Regel steht die Nachricht, aus der sie stammt.

## Entitäten

| Entität | Attribute |
|---|---|
| Patient | patient_id, name, geburtsdatum, versicherungsart (GKV, PKV), versichertennr bzw. praxis_patientennr, kontostatus (angefragt, eingeladen, aktiv), einwilligung_email, einwilligung_sms, online_gesperrt, betreuende_aerztin → Mitarbeiter |
| Mitarbeiter | mitarbeiter_id, name, rolle (Ärztin, MFA) |
| Terminart | terminart_id, name (Vorsorge, Akut, Beratung, Impfung, Blutabnahme, Erstgespräch), dauer_min, online_buchbar, fragebogen (optional) |
| Termin | termin_id, patient → Patient, aerztin → Mitarbeiter, terminart → Terminart, beginn, status (gebucht, abgesagt, kurzfristig abgesagt, nicht erschienen, entschuldigt, wahrgenommen), notiz (Tresen), notiz_aerztin (nur Ärztin) |
| Zeitfenster | zeitfenster_id, typ (Sprechzeit, Abwesenheit, Schließtag), mitarbeiter → Mitarbeiter (leer bei Schließtag), von, bis |
| Wiederholungsrezept | rezept_id, patient → Patient, medikament, aktiv |
| Rezeptanfrage | anfrage_id, rezept → Wiederholungsrezept, angefragt_am, status (angefragt, freigegeben, nicht freigegeben, abgeholt), freigegeben_von → Mitarbeiter |

Ärztin und MFA sind eine Entität mit Rolle. Der No-Show-Zähler wird nicht gespeichert, sondern aus den Terminen mit Status „nicht erschienen“ der letzten 12 Monate berechnet.

## Beziehungen

- Ärztin n:m Terminart (bietet an, Attribut qualifikation; Impfung nur Dr. Demir, N3)
- Patient n:m Ärztin über Termin
- Terminart 1:n Termin
- Mitarbeiter 1:n Zeitfenster
- Ärztin 1:n Patient (betreut hauptsächlich, N9)
- Patient 1:n Wiederholungsrezept
- Wiederholungsrezept 1:n Rezeptanfrage
- Ärztin 1:n Rezeptanfrage (gibt frei)

## Business Rules

- **BR1** Online buchen nur identifizierte Patienten: GKV mit Versichertennummer, PKV mit Praxis-Patientennummer. Neupatienten können ein Erstgespräch nur anfragen, die MFA legt sie an und lädt sie ein. Keine Selbstregistrierung (Nachricht 4, 17)
- **BR2** Online anfragbar sind nur laufende Wiederholungsrezepte. Jede Anfrage gibt einzeln eine Ärztin frei, Standard ist der nächste Werktag. Ist die betreuende Ärztin abwesend, übernimmt eine anwesende (Nachricht 8, 9)
- **BR3** E-Mail und SMS nur mit schriftlicher Einwilligung und nur über den zugestimmten Kanal (Nachricht 6, 12)
- **BR4** 2 No-Shows in 12 Monaten (rollierend): schriftliche Erinnerung, 3 No-Shows: Online-Buchung gesperrt. Eine aktive Absage zählt nie, Notfälle setzt die MFA auf „entschuldigt“, die Sperre hebt nur die MFA auf (Nachricht 7, 15, 19)
- **BR5** Akutslots nur am selben Tag und nur über die MFA (Telefon oder Walk-in), nie online (Nachricht 2, 10)

## Rollen und Systemlogik

- Patient sieht nur eigene Termine und den eigenen Rezept-Status. MFA sieht alle Termine und Stammdaten, aber keine Notizen der Ärztin und keine Fragebogen-Inhalte. Ärztin sieht alles (Nachricht 6, 13)
- Ein Slot wird nur angeboten, wenn die Ärztin die Terminart anbietet, Sprechzeit hat und nicht abwesend ist. Ein Schließtag sperrt die ganze Praxis (Nachricht 3, 14)
- Gleiche Slots für alle, wer zuerst bucht, bekommt den Termin (Nachricht 4)
- Absagen kann der Patient in der App, Verschieben gibt es nicht (Nachricht 15)
- Das PVS bleibt. Termine werden in beide Richtungen in Echtzeit synchronisiert (Nachricht 16)
- Gesundheitsdaten nur in der Praxis-Datenbank, nicht in Logs oder externen Systemen (Nachricht 6, 12)

## Widersprüche

- **W1** Rezeptfreigabe „mittags, Abholung nachmittags“ (Nachr. 2) vs. „nächster Werktag“ (Nachr. 8) → aufgelöst: Standard ist der nächste Werktag, am selben Tag nur als Bonus (Nachr. 9)
- **W2** Akutsprechstunde „offen für alle“ vs. „MFA filtert vorher“ (Nachr. 2) → aufgelöst: Die MFA triagiert, Akutslots sind nicht online buchbar (Nachr. 10)
- **W3** Rezept-Workflow „erste Version“ (Nachr. 11) vs. „danach“ (Nachr. 18) → aufgelöst: erste Ausbaustufe, 2–3 Wochen nach Start (Nachr. 20)

## Neue Anforderung

- **Online-Anamnese** (Nachricht 11–13): Nach einer Online-Buchung bekommt der Patient in der App einen Fragebogen passend zur Terminart. Ausfüllen ist freiwillig. Den Inhalt sieht nur die Ärztin, die MFA nur den Status → Terminart.fragebogen, Termin.fragebogen_status und Termin.fragebogen_antworten

## Prioritäten

1. Online-Buchung, Identifikation, Sprechzeiten und Abwesenheiten, PVS-Sync, DSGVO (v1, erster Tag)
2. Rezept-Workflow (v1, 2–3 Wochen später)
3. No-Show-Sperre, Erinnerungen mit Einwilligung, Akutslot-Steuerung für die MFA (v2)
4. Online-Anamnese (später)

Nicht im Scope: Abrechnung und Kasse (Nachricht 5), Verschieben, Selbstregistrierung

## Offene Fragen

Vom Kunden bewusst vertagt, deshalb im MVP mit Annahme gelöst:

- **Welches PVS?** Will der Kunde erst bei der technischen Abstimmung nennen (Nachricht 16) → im MVP wird das PVS simuliert
- **Welche Terminarten sind online buchbar und bekommen einen Fragebogen?** „Legen wir gemeinsam fest“ (Nachricht 12) → online buchbar: Vorsorge, Beratung, Impfung; Fragebogen für Vorsorge und Impfung

## Annahmen

Im Interview nicht angesprochen, für das MVP so entschieden:

- Ein Termin hat genau eine Terminart
- Termine sind bis 8 Wochen im Voraus buchbar
- Fragebogen-Antworten bleiben in der App-Datenbank und gehen nicht ins PVS, weil Gesundheitsdaten nicht in externe Systeme dürfen (Nachricht 12)
