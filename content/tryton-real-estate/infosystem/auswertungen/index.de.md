---
title: "11.1 Auswertungen"
weight: 10
---

{{% callout style="info" %}}
Weitere Auswertungen sind in Arbeit.
{{% /callout %}}

## Bemessungen

![Liste Bemessungen](bemessungen.png)

Menü **Immobilien › Stammdaten › Bemessungen** öffnen. Die Liste zeigt die Bemessungen der Objekte, z. B. Wohnflächen, und summiert die Werte am Ende der Liste. Erfasst werden Bemessungen am jeweiligen Objekt im Reiter **Bemessungen**, siehe [5.1 Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}).

| Feld | Beschreibung |
|------|--------------|
| Gesellschaft | Gesellschaft, deren Bemessungen angezeigt werden. Pflichtfeld. |
| Wirtschaftseinheit | Schränkt die Liste auf die Objekte dieser Wirtschaftseinheit ein. |
| Objekt | Schränkt die Liste auf dieses Objekt und alle darunterliegenden Objekte ein; hat Vorrang vor der Wirtschaftseinheit. |
| Bemessungstyp | Zeigt nur Bemessungen dieses Typs, z. B. Wohnfläche. |
| Stichtag | Es werden nur die Bemessungen angezeigt, die am Stichtag gültig sind: „von“-Datum am oder vor dem Stichtag und noch nicht durch einen neueren Eintrag desselben Objekts und Bemessungstyps abgelöst. Vorgabe ist das heutige Datum. |

Die Spalten zeigen Gesellschaft, Wirtschaftseinheit, Immobilienobjekt, gültig von, Bemessungstyp, Wert und Symbol (Einheit).

{{% callout style="note" title="Hinweis zur Summe" %}}
Die Summe am Ende der Liste ergibt damit den Stand zum Stichtag, z. B. die gesamte Wohnfläche.
{{% /callout %}}

## Adressen

![Liste Adressen](adressen.png)

Menü **Immobilien › Stammdaten › Adressen › Adressen** öffnen. Die Liste zeigt die Anschriften der Immobilienobjekte mit Straße, PLZ, Stadt, Land und Unterabteilung (z. B. Bundesland). Diese Objektadressen sind unabhängig von den Adressen der Geschäftspartner. Sie werden am Objekt im Feld **Adresse** (Reiter Allg. Daten) zugeordnet, siehe [5.1 Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}); dort kann eine neue Adresse auch direkt angelegt werden. Über **+** (Neu) lassen sich Adressen auch in der Liste anlegen; die Maske entspricht der Tryton-Adresse (Straße und Hausnummer, PLZ, Stadt, Land, Unterabteilung).
