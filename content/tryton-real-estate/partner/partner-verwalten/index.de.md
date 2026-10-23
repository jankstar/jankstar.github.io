---
title: "3.1 Partner verwalten"
description: "Parteien (Geschäftspartner) in Tryton verwalten: Liste, Maske, Adressen, Kontaktinformationen und Kategorien – Überblick für die Immobilienverwaltung."
weight: 10
---

{{% callout style="info" title="Überblick" %}}
Parteien und Unternehmen gehören zum Tryton-Standard, nicht zum Immobilien-Modul. Diese Seite gibt nur einen Überblick über die Masken und die Felder, die für die Immobilienverwaltung wichtig sind; die übrigen Reiter sind in der [Tryton-Dokumentation](https://docs.tryton.org) beschrieben.
{{% /callout %}}

Geschäftspartner heißen in Tryton **Parteien** – Personen und Firmen gleichermaßen: Mieter, Eigentümer, Lieferanten, Dienstleister, Behörden und auch die eigene Gesellschaft. Eine Partei wird einmal angelegt und dann in allen Bereichen verwendet, in Verträgen, Rechnungen und an den Immobilien-Objekten.

## Einstieg und Liste

![Menü Parteien und Liste der Parteien](einstieg-liste.png)

Das Menü **Parteien** enthält:

| Menü | Inhalt |
|------|--------|
| Einstellungen | Grundeinstellungen der Parteien, z. B. Nummernkreis |
| Parteien | Liste und Maske der Parteien |
| Identifikatoren | Alle Identifikatoren, z. B. Steuernummern |
| Adressen | Alle Adressen |
| Kontaktinformationen | Telefon, E-Mail, Website usw. |
| Beziehungen | Beziehungen zwischen Parteien |
| Kategorien | Kategorien zur Gruppierung von Parteien |

Die Liste **Parteien** zeigt **Code Partei**, **Name**, **Steueridentifikator** sowie **Forderungen heute** und **Verbindlichkeiten heute**. Mit **Filter** wird gesucht, ein Doppelklick öffnet die Maske, **+** legt eine neue Partei an.

Das Menü **Unternehmen** mit **Unternehmen** und **Mitarbeiter** ist in [3.2 Unternehmen und Mitarbeiter]({{< relref "/tryton-real-estate/partner/unternehmen" >}}) beschrieben.

## Maske und Reiter Allgemein

![Partei, Reiter Allgemein](maske-allgemein.png)

Im Kopf der Maske stehen **Name**, **Code Partei** (wird beim Speichern vergeben), **Aktiv**, **Sprache** (Sprache der Korrespondenz), **Reihenfolge** und **Anrede**, z. B. „Sehr geehrte Damen und Herren“ für die Briefe. Darunter folgen die Reiter **Allgemein**, **Lieferant**, **Identifikatoren**, **Banking**, **Beziehungen**, **Buchhaltung**, **Lager** und **Verkauf**.

Der Reiter **Allgemein** enthält:

- **Adressen:** eine oder mehrere Anschriften mit Straßenname, Postleitzahl, Stadt und Land; weitere Felder wie Gebäudename und -nummer, Etage, Postfach oder Region sind optional. Mit den Kennzeichen **Rechnung** und **Lieferadresse** wird festgelegt, wofür die Adresse verwendet wird; **Zustellzusatz** nimmt z. B. „c/o“ oder einen abweichenden Namen auf. Mit **+** wird eine weitere Adresse angelegt, mit den Pfeilen zwischen den Adressen geblättert.
- **Kontaktinformationen:** Telefon, Mobiltelefon, E-Mail, Website usw. mit Typ, Wert und Name.
- **Kategorien:** Zuordnung zu Kategorien, siehe [3.4 Beziehungen, Kontaktinformationen und Kategorien]({{< relref "/tryton-real-estate/partner/beziehungen" >}}).
