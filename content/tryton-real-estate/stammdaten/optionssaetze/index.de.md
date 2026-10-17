---
title: "5.6 Optionssätze"
weight: 60
---

Der Optionssatz gibt an, zu welchem Anteil die Vorsteuer aus Eingangsrechnungen abziehbar ist, weil der Eigentümer bei der Vermietung zur Umsatzsteuer optiert hat (§ 9 UStG). Er wird je Objekt, Abrechnungseinheit und Kostensammler mit Gültigkeitsdatum geführt und bei der Erfassung von Eingangsrechnungen automatisch vorgeschlagen. Gepflegt wird die Methode im Reiter **Optionssatz** des Objekts, siehe [5.1 Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}).

| Optionssatzmethode | Optionssatz |
|--------------------|-------------|
| optiert nicht (0,0 %) fix | immer 0 % – die Vorsteuer ist Aufwand |
| optiert (100,0 %) fix | immer 100 % – die Vorsteuer ist voll abziehbar |
| Optionssatz fix nach Wert | fester Prozentsatz, im Reiter Optionssatz eingetragen |
| dynamisch über Bemessung | wird berechnet: Durchschnitt der Optionssätze der Mietobjekte, gewichtet mit einer Bemessung (z. B. Wohn-/Nutzfläche); nicht für Mietobjekte selbst |

Ist bei der Gesellschaft eingestellt, dass die Vorsteuer immer als Aufwand gebucht wird, gibt es keinen Optionssatz; der Reiter ist dann ausgeblendet.

## Berechnung Optionssatz

Der Assistent berechnet die Optionssätze für eine Auswahl neu und legt bei Änderungen einen neuen Eintrag an. Er wird z. B. nach einem Mieterwechsel zwischen Wohnung und Gewerbe oder nach geänderten Flächen ausgeführt.

{{% callout style="info" title="Automatisch als Job" %}}
Dieselbe Berechnung kann auch periodisch laufen: als Job **Optionssatz aktualisieren**, z. B. monatlich am 1. Er berechnet alle Wirtschaftseinheiten der Gesellschaft zum aktuellen Datum, der neue Satz gilt ab dem Monatsersten. Eingerichtet wird der Job in [2.5 Jobs und periodische Arbeiten]({{< relref "/tryton-real-estate/system/jobs" >}}).
{{% /callout %}}

### Auswahl

![Assistent Berechnung Optionssatz – Auswahl](wizard-berechnung.png)

Menü **Immobilien › Stammdaten › Berechnung Optionssatz** öffnen.

| Feld | Beschreibung |
|------|--------------|
| Unternehmen | Gesellschaft, deren Objekte berechnet werden. Pflichtfeld. |
| Base Objects (Objekte) | Wirtschaftseinheiten, Gebäude, Grundstücke oder Mietobjekte. Ausgewählte Wirtschaftseinheiten, Gebäude und Grundstücke werden um alle darunterliegenden Objekte erweitert, eine Wirtschaftseinheit zusätzlich um ihre Abrechnungseinheiten und Kostensammler. |
| Abrechnungseinheiten | Abrechnungseinheiten; sie werden um ihre Kostensammler erweitert. |
| Kostensammler | Einzelne Kostensammler. |
| Stichtag | Datum, zu dem gerechnet wird. Der neue Optionssatz gilt ab dem **Ersten dieses Monats**. Vorschlag: heute. |

Bleiben die drei Listen leer, werden **alle Wirtschaftseinheiten** der Gesellschaft berechnet.

### Bestätigen

![Assistent Berechnung Optionssatz – Bestätigen](wizard-bestaetigen.png)

Der zweite Schritt zeigt **Stichtag** und **Effektives Datum** (Monatserster, ab dem der neue Satz gilt), die Anzahl der ausgewählten Objekte, Abrechnungseinheiten und Kostensammler sowie die **Gesamtzahl der zu verarbeitenden Objekte** nach der Erweiterung. Im Beispiel werden aus 2 Wirtschaftseinheiten 102 Objekte. Mit **In Ausführung** wird gerechnet.

So wird je Objekt gerechnet:

- **fix 0 % / fix 100 % / fix nach Wert:** Der Satz wird direkt übernommen.
- **dynamisch über Bemessung:** Jedes aktive Mietobjekt darunter geht mit seiner Bemessung zum Stichtag als Gewicht und mit seinem eigenen Optionssatz ein; Ergebnis ist der gewichtete Durchschnitt, auf 2 Nachkommastellen gerundet. Ein nicht aktives Objekt schließt seinen ganzen Teilbaum aus. Bei Abrechnungseinheiten und Kostensammlern zählen deren zugeordnete Mietobjekte.
- Objekte, die nicht **Aktiv** sind, sowie Abrechnungseinheiten und Kostensammler im Status Entwurf oder abgerechnet werden nicht verarbeitet.

### Ergebnis

![Assistent Berechnung Optionssatz – Ergebnis](wizard-ergebnis.png)

| Feld | Bedeutung |
|------|-----------|
| Created (Angelegt) | Neuer Optionssatz-Eintrag, gültig ab dem Monatsersten. |
| Updated (Aktualisiert) | Zum selben Monatsersten gab es schon einen Eintrag; er wurde überschrieben. |
| Unchanged (Unverändert) | Der berechnete Satz entspricht dem geltenden; es wird nichts geschrieben. |
| Übersprungen | Es ließ sich kein Satz berechnen, z. B. bei „dynamisch über Bemessung“ ohne Bemessungen. |
| Details | Je Objekt eine Zeile mit Ergebnis und Satz, z. B. „Musterstraße 1-4 (base_object): unchanged -> 33.33 %“. |

Im Beispiel haben die Wohnungen 0 %, die Gewerbeeinheit 100 % und Gebäude und Wirtschaftseinheit den gewichteten Satz von 33,33 %.

## Liste Optionssätze

![Menü und Liste Optionssätze](einstieg-liste.png)

Menü **Immobilien › Stammdaten › Liste Optionssätze** öffnen. Die Liste zeigt alle gebuchten Optionssätze mit Immobilienobjekt, Kostensammler bzw. Abrechnungseinheit, **Gültig ab** und **Optionssatz** in Prozent. Über die Felder **Immobilienobjekt**, **Abrechnungseinheit** und **Kostensammler** lässt sich die Liste einschränken. Die Liste dient nur zur Auswertung und kann nicht bearbeitet werden; Einträge entstehen über den Assistenten oder im Reiter **Optionssatz** des Objekts.

Welcher Optionssatz bei einer Eingangsrechnung vorgeschlagen wird, richtet sich nach dem genauesten Bezug der Rechnungszeile – Objekt vor Kostensammler vor Abrechnungseinheit vor Wirtschaftseinheit – und nach dem Rechnungsdatum: Es gilt der letzte Eintrag mit **Gültig ab** am oder vor diesem Datum. Siehe [4.1 Rechnungen]({{< relref "/tryton-real-estate/rechnungswesen/rechnungen" >}}).
