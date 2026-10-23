---
title: "1.1 Über dieses Handbuch"
description: "Zweck, Zielgruppen, Aufbau und Darstellung des Handbuchs zur Immobilienverwaltung mit Tryton; Hinweis zur Entstehung mit KI-Unterstützung."
weight: 10
---

Dieses Handbuch beschreibt die Arbeit mit dem Tryton-Modul **jankstar_real_estate** für die Immobilienverwaltung – von den Stammdaten über Verträge, Mietanpassungen und Betriebskosten bis zu Instandhaltung, Aufgaben und Korrespondenz.

## Für wen

- **Sachbearbeitung:** Hauptzielgruppe. Das Handbuch begleitet durch die Prozesse der Immobilienverwaltung von A bis Z.
- **Administration:** Installation, Module, Benutzer, Nummernkreise und Einstellungen in Kapitel 2.
- **Buchhaltung:** Grundlagen in Kapitel 4 für alle, die die Buchhaltung der Immobilien mit erledigen; die automatischen Buchungen des Moduls sind jeweils beim Prozess erläutert.

## Aufbau

| Kapitel | Inhalt |
|---------|--------|
| 1 Einführung | dieses Handbuch, Tryton, Bedienung, das Modul im Überblick |
| 2 System und Voraussetzungen | Installation, Benutzer, Nummernkreise, Einstellungen, Jobs |
| 3 Geschäftspartner und Gesellschaften | Parteien, Unternehmen, Partnerrollen |
| 4 Rechnungswesen | Rechnungen, Zahlungen, Buchungen, Abschluss |
| 5 Immobilien-Stammdaten | Wirtschaftseinheit, Gebäude, Mietobjekte, Equipment, Zähler, Optionssätze |
| 6 Verträge | Vertrag, Konditionen, periodische Buchung, Mietanpassungen |
| 7 Betriebskosten | Abrechnungseinheiten bis zur Betriebskostenabrechnung |
| 8 Instandhaltung | Prüfpläne, Prüfungen, Mängel |
| 9 Bau und Projektsystem | Projekte |
| 10 Aufgaben und Prozesse | Aufgaben und Prozesse bearbeiten |
| 11 Infosystem und Korrespondenz | Auswertungen und Vorlagen |
| Glossar | Fachbegriffe deutsch/englisch |

Das Handbuch wird laufend ergänzt; Kapitel und Seiten ohne Inhalt sind noch ausgeblendet.

## Darstellung

- Jede Seite beginnt mit dem Einstieg (Menü, Liste) und beschreibt dann Maske, Reiter und Aktionen; die Abbildungen sind je Seite nummeriert.
- **Menüpfade** stehen fett mit „›“, z. B. **Immobilien › Stammdaten › Wirtschaftseinheit**.
- **Feld- und Schaltflächennamen** stehen fett, wie sie in der Oberfläche erscheinen. Die englische Fassung nennt den deutschen Namen und die Übersetzung in Klammern.
- Themen aus dem Tryton-Standard (Parteien, Unternehmen, Benutzer, Rechnungswesen) sind nur im Überblick beschrieben, soweit sie für die Immobilienverwaltung wichtig sind; Einzelheiten stehen in der [Tryton-Dokumentation](https://docs.tryton.org).
- Die Beispiele stammen aus einer Demo-Datenbank; Namen und Nummern sind erfunden.

## Hinweise zu Entstehung und Haftungsausschluss

Die Texte und Bildbearbeitungen dieses Handbuchs wurden mit Unterstützung von KI (Claude, Anthropic) erstellt. Inhalte und Angaben wurden vom Autor nach bestem Wissen fachlich geprüft.

Ungeachtet der Sorgfalt, die auf die Erstellung von Text, Abbildungen und Programmcode verwendet wurde, werden für mögliche Fehler und deren Folgen keine juristische Verantwortung oder irgendeine Haftung übernommen.

TRYTON® ist eine eingetragene Marke der Tryton Foundation.

{{% callout style="warning" %}}
Installation und Konfiguration in Kapitel 2 beschreiben ein Entwicklungs- bzw. Testsystem. Für die Sicherheit seiner Systeme ist jeder Betreiber selbst verantwortlich.
{{% /callout %}}
