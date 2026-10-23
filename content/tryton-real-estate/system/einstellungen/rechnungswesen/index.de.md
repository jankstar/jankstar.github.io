---
title: "2.5.1 Rechnungswesen"
description: "Rechnungswesen in Tryton einrichten: Kontenrahmen der Wohnungswirtschaft (WoWi), Einstellungen, Geschäftsjahr, Nummernkreise, Bankkonten."
weight: 10
---

{{% callout style="info" %}}
Diese Seite ist noch nicht vollständig: Steuern, Fälligkeit, Journale, Bank und Zahlungsverkehr sowie RE-Rechnungswesen werden ergänzt.
{{% /callout %}}

## Kontenplan

Menü **Rechnungswesen › Einstellungen › Konten › Konten**. Der Kontenplan wird bei der Ersteinrichtung aus einer Vorlage angelegt, für die Immobilienverwaltung aus dem **Kontenrahmen der Wohnungswirtschaft (WoWi)**, siehe [2.1 Systemvoraussetzungen und Installation]({{< relref "/tryton-real-estate/system/voraussetzungen" >}}).

![Rechnungswesen, Konten](kontenplan.png)

Die Konten sind als Baum gegliedert: Kontenklassen 0 bis 9, darunter Gruppen und die einzelnen Konten. Im Bild ist die Klasse 2 aufgeklappt mit den **Mietforderungen**, u. a. 2000 Mietenkontokorrent-Sammelkonto, 2001 Forderungen an ehemalige Mieter, 2002 Forderungen aus abgerechneten Umlagen sowie den Wertberichtigungen 2090 und 2095. Unter **Kontentypen** steht die Gliederung für Bilanz und GuV, unter **Konto Abstimmungsregeln** die Regeln für den Ausgleich offener Posten.

## Einstellungen

Menü **Rechnungswesen › Einstellungen › Einstellungen**. Hier stehen die Vorgaben des Rechnungswesens für das Unternehmen; die meisten werden bei der Ersteinrichtung mit dem Kontenplan gesetzt, siehe [2.1 Systemvoraussetzungen und Installation]({{< relref "/tryton-real-estate/system/voraussetzungen" >}}).

![Rechnungswesen, Einstellungen](einstellungen.png)

| Bereich | Einstellungen (Beispiel im Bild) |
|---------|----------------------------------|
| Partei | Standardkonto Forderungen (2000 Mietenkontokorrent-Sammelkonto) und Verbindlichkeiten (4420), Standard-Zahlungsbedingung Verkauf |
| Artikel | Standardertragskonto (6000 Sollmieten) und Standardaufwandskonto (8050) |
| Rechnung | Kunden-Zahlungsreferenznummer, Standardsteuerregel Kunde und Lieferant, Steuerrundung (pro Dokument) |
| Buchungssatz | Nummernkreis Abstimmung |
| Währungsumrechnung | Journal, Soll- und Habenkonto für Kursdifferenzen |
| Mahnung | Standardmahnprozess, Ersatzbenutzer |
| Rechnungsabgrenzung | Konten für passive und aktive Rechnungsabgrenzung |
| Anlage | Nummernkreis, Abschreibungsbuchungsfrequenz; Tag und Monat der Anlagenbuchungen |
| Zahlung, SEPA | Nummernkreise für Zahlung, Zahlungslauf und SEPA-Mandat |

Die Nummernkreise sind in [2.4 Nummernkreise]({{< relref "/tryton-real-estate/system/nummernkreise" >}}) beschrieben.

## Nummernkreise

Für Buchungssätze und Rechnungen werden je Unternehmen **strikte Nummernkreise** benötigt, siehe [2.4 Nummernkreise]({{< relref "/tryton-real-estate/system/nummernkreise" >}}). Zugeordnet werden sie im Geschäftsjahr.

## Geschäftsjahr

Menü **Rechnungswesen › Einstellungen › Geschäftsjahre › Geschäftsjahre**. Für jedes Jahr wird je Unternehmen ein Geschäftsjahr angelegt, bevor darin gebucht werden kann – auch die periodische Buchung der Verträge braucht ein offenes Geschäftsjahr.

![Rechnungswesen, Geschäftsjahr mit Buchungszeiträumen](geschaeftsjahr.png)

- Kopf: **Name** (z. B. 2025), **Unternehmen**, **Startdatum** und **Enddatum**; unten der **Status** (Offen, später Geschlossen bzw. Gesperrt).
- Reiter **Buchungszeiträume:** die Perioden des Jahres, üblich monatlich (2025-01 bis 2025-12) mit Typ **Standard** und Status **Offen**. Gebucht wird nur in offene Buchungszeiträume; abgeschlossene Monate werden geschlossen.
- Reiter **Nummernkreise**, siehe unten; daneben der Reiter **Steuern**.

![Geschäftsjahr, Reiter Nummernkreise](geschaeftsjahr-nummernkreise.png)

Im Reiter **Nummernkreise** werden die strikten Nummernkreise des Jahres zugeordnet, siehe [2.4 Nummernkreise]({{< relref "/tryton-real-estate/system/nummernkreise" >}}):

- **Nummernkreis Buchungssatz** für die Nummern der gebuchten Buchungssätze (im Bild 25/1);
- **Nummernkreise Rechnung** je Unternehmen und optional je Buchungszeitraum: Lieferantenrechnungen, Lieferantenstornorechnungen, Kundenrechnungen und Kundenstornorechnungen (25/2 bis 25/5).

Unter **Buchungszeiträume** stehen alle Perioden aller Jahre; **Jahresübernahme** legt zum Jahreswechsel das nächste Geschäftsjahr auf Basis des bisherigen an, mit Buchungszeiträumen und neuen Nummernkreisen.

## Steuern

## Fälligkeit

## Journale

## Bank und Zahlungsverkehr

Überweisungen und Lastschriften werden zuerst gegen ein Zwischenkonto **Bankbewegung (Geldtransit)** gebucht; erst der Kontoauszug der Bank bucht vom Zwischenkonto auf das Bankkonto. Was auf dem Zwischenkonto offen ist, sind Zahlungen, die die Bank noch nicht bestätigt hat. Einrichtung in der richtigen Reihenfolge und Buchungsablauf: siehe [4.2 Zahlungen]({{< relref "/tryton-real-estate/rechnungswesen/zahlungen" >}}).

### Bankkonten im Kontenplan

![Kontenplan, Bankkonten](bankkonten.png)

Unter **Klasse 2 › Flüssige Mittel › Guthaben bei Kreditinstituten** werden neben dem Vorlagenkonto 2740 eigene Konten angelegt (Menü **Rechnungswesen › Einstellungen › Konten › Konten**, **+**):

| Konto | Zweck | Wichtige Einstellungen |
|-------|-------|------------------------|
| 2741 Bankkonto (je Bank ein Konto, im Bild Berliner Sparkasse) | Kontobewegungen laut Kontoauszug | Kontotyp wie 2740 |
| 2749 Bankbewegung (Geldtransit) | Zwischenkonto für Überweisungen und Lastschriften | Kontotyp wie 2740, **Abstimmen** angehakt, keine Partei erforderlich |

Vorlagenkonten wie 2740 werden nicht umbenannt: Beim Aktualisieren des Kontenplans nach einem Modul-Update holt Tryton Name und Einstellungen wieder aus der Vorlage.

## RE-Rechnungswesen
