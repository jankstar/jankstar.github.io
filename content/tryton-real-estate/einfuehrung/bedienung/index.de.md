---
title: "1.3 Bedienung von Tryton"
description: "Bedienung von Tryton im Web-Client sao: Menü, Liste und Filter, Symbolleiste und das Menü an der Überschrift der Registerkarte."
weight: 30
---

Die Bedienung ist in allen Bereichen von Tryton gleich. Die Bilder zeigen den Web-Client sao; der Desktop-Client ist gleich aufgebaut.

## Aufbau des Fensters

![Fenster mit Menü, Liste und Symbolleiste](fenster-liste.png)

- **Menü (links):** die Bereiche, z. B. Parteien, Immobilien, Rechnungswesen, Verwaltung. Mit **›** werden Ordner aufgeklappt, ein Klick auf einen Eintrag öffnet ihn als **Registerkarte** rechts. Mit dem **Stern** wird ein Eintrag zu den Favoriten hinzugefügt.
- **Aktion (oben):** Suchfeld für Menüeinträge und Favoriten – Name eintippen und auswählen, ohne sich durch das Menü zu klicken. **≡ Tryton** blendet das Menü ein und aus.
- **Registerkarten:** Jeder geöffnete Menüeintrag erscheint oben als Karte; **×** schließt sie.
- **Benutzermenü (oben rechts):** Name und Unternehmen [Währung], darunter Benachrichtigungen, **Einstellungen…** (z. B. Sprache, aktuelles Unternehmen), **Hilfe…** und **Abmelden**.

## Liste und Filter

Die meisten Menüeinträge öffnen zuerst eine **Liste**. Ein Doppelklick auf eine Zeile öffnet die Maske des Datensatzes.

![Liste mit Filter und Symbolleiste](liste-filter.png)

- **Filter:** Suchbegriff oder Bedingung eingeben, z. B. einen Namen oder `Name: Immo*`; **Lupe** sucht, beim Tippen werden passende Felder vorgeschlagen.
- **Stern:** den aktuellen Filter als Lesezeichen speichern; **Lesezeichen-Symbol:** gespeicherte Filter aufrufen.
- **Archiv-Symbol:** auch inaktive (archivierte) Datensätze anzeigen.
- **‹ ›** neben dem Filter: in langen Listen seitenweise blättern.
- **≡** links im Spaltenkopf: Spalten ein- und ausblenden; Klick auf eine Spaltenüberschrift sortiert.
- **Kästchen:** Datensätze markieren – Aktionen, Drucken usw. gelten dann für alle markierten.

## Symbolleiste

![Symbolleiste](symbolleiste.png)

| Schaltfläche | Funktion |
|--------------|----------|
| <img src="/images/tryton-icons/ansicht.png" alt="Ansicht wechseln" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Ansicht wechseln | zwischen Liste und Maske umschalten |
| <img src="/images/tryton-icons/vorheriger.png" alt="Vorheriger" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> <img src="/images/tryton-icons/naechster.png" alt="Nächster" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Vorheriger / Nächster | zum vorherigen bzw. nächsten Datensatz |
| <img src="/images/tryton-icons/zaehler.png" alt="Zähler" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Zähler | aktueller Datensatz / Anzahl in der Liste |
| <img src="/images/tryton-icons/neu.png" alt="Neu" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Neu | neuen Datensatz anlegen |
| <img src="/images/tryton-icons/speichern.png" alt="Speichern" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Speichern | Änderungen speichern |
| <img src="/images/tryton-icons/neuladen.png" alt="Neu laden" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Neu laden/Rückgängig | neu laden bzw. ungespeicherte Änderungen verwerfen |
| <img src="/images/tryton-icons/anhang.png" alt="Anhang" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Anhang | Anhänge (Dateien, Links) anzeigen und hinzufügen; die Zahl am Symbol zeigt vorhandene Anhänge |
| <img src="/images/tryton-icons/notiz.png" alt="Notiz" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Notiz | Notizen zum Datensatz |
| <img src="/images/tryton-icons/aktion.png" alt="Aktion" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Aktion | Assistenten und Aktionen des Datensatzes, z. B. Berechnungen |
| <img src="/images/tryton-icons/verknuepfen.png" alt="Verknüpfen" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Verknüpfen | zu verbundenen Datensätzen springen, z. B. vom Vertrag zu Rechnungen |
| <img src="/images/tryton-icons/drucken.png" alt="Drucken" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Drucken | Berichte und Schreiben drucken |
| <img src="/images/tryton-icons/email.png" alt="E-Mail" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> E-Mail | E-Mail mit Bericht als Anhang erstellen |

## Menü an der Überschrift

![Menü an der Überschrift der Registerkarte](menue-ueberschrift.png)

Ein Klick auf die **Überschrift** der Registerkarte (z. B. **Parteien ▾**) öffnet ein Menü mit allen Funktionen der Symbolleiste und einigen weiteren:

| Gruppe | Funktionen |
|--------|------------|
| Navigation | Ansicht wechseln, Vorheriger, Nächster, Suchen |
| Bearbeiten | Neu, Speichern, Neu laden/Rückgängig, Duplizieren, Löschen |
| Protokoll | **Logs anzeigen…** (wer hat wann angelegt bzw. geändert), **Bearbeitungen anzeigen…** (Änderungshistorie, falls für das Modell aktiv) |
| Zusatz | Anhang, Notiz, Aktion |
| Ausgabe | Verknüpfen, Drucken, E-Mail… |
| Daten | **Export** (Datensätze z. B. als CSV ausgeben), **Import** (Datensätze aus CSV einlesen) |
| | Registerkarte schließen |

**Duplizieren**, **Löschen**, **Logs**, **Export** und **Import** gibt es nur in diesem Menü.
