---
title: "5.2 Gebäude und Grundstücke"
weight: 20
---

Gebäude und Grundstücke sind die Ebene unter der [Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}). Beide werden mit derselben Maske gepflegt wie die Wirtschaftseinheit; hier sind nur die Unterschiede beschrieben.

## Gebäude

### Einstieg

![Menü und Liste Gebäude](einstieg-menue-gebaeude.png)

Menü **Immobilien › Stammdaten › Gebäude** öffnen. Die Liste zeigt die Gebäude der oben angezeigten **Gesellschaft**. Über das Feld **Wirtschaftseinheit** lässt sich die Liste auf eine Wirtschaftseinheit einschränken. Reiter, Neu anlegen und Bearbeiten wie bei der Wirtschaftseinheit.

### Anlegen und Bearbeiten

![Maske Gebäude mit Reiter Allg. Daten](maske-gebaeude.png)

Der Kopf der Maske entspricht der [Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}). Zusätzlich bzw. abweichend:

| Feld | Beschreibung |
|------|--------------|
| Typ | Hier **Gebäude**. |
| Wirtschaftseinheit | Wirtschaftseinheit, zu der das Gebäude gehört; wird aus dem übergeordneten Objekt übernommen und kann nicht geändert werden. |
| Übergeordnetes Objekt | Objekt, unter dem das Gebäude hängt: eine Wirtschaftseinheit oder ein anderes Gebäude. Pflichtfeld. |
| Nr. | Wird aus der Nummer des übergeordneten Objekts und der Reihenfolge gebildet, z. B. **10/10**. |

Der Reiter **Allg. Daten** enthält Startdatum, Endedatum, Adresse, Anzahl Unterobjekte und Bemerkung wie bei der Wirtschaftseinheit; die Felder zum Wohnungsmarkt und zur Kappungsgrenze gibt es nur dort.

#### Reiter Gebäude

![Reiter Gebäude](maske-reiter-gebaeude.png)

| Feld | Beschreibung |
|------|--------------|
| Baujahr | Baujahr des Gebäudes. |
| Anzahl Etagen | Anzahl der Etagen des Gebäudes. |

#### Weitere Reiter

Die Reiter **Bemessungen**, **Optionssatz**, **Partner**, **Unterobjekte**, **Prüfungen**, **Mietspiegel** und **Aufgaben und Prozesse** sind aufgebaut wie bei der [Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}). Für Gebäude gilt:

- **Partner:** Für Gebäude ist keine Rolle ausgeliefert; Hausmeister und Verwalter werden an der Wirtschaftseinheit zugeordnet.
- **Unterobjekte:** Gebäude (z. B. Seitenflügel oder Hinterhaus), Mietobjekte und Equipment. Unterobjekte eines Gebäudes sind vor allem die Mietobjekte, siehe [5.3 Mietobjekte]({{< relref "/tryton-real-estate/stammdaten/mietobjekt" >}}).
- **Prüfungen:** Hier werden die Prüfpläne des Gebäudes angelegt. Beide ausgelieferten Prüfarten, jährliche Objektbegehung und Rauchwarnmelderprüfung, gelten je Gebäude, siehe [8.1 Prüfpläne]({{< relref "/tryton-real-estate/instandhaltung/pruefplaene" >}}).
- **Aufgaben und Prozesse:** Ist ein Endedatum eingetragen, erzeugt die Regel **Objekt endet** hier eine Aufgabe.

### Aktionen, Verknüpfungen und Drucken

| Menü | Eintrag | Bedeutung |
|------|---------|-----------|
| Aktionen | Neue Aufgabe | Legt eine Aufgabe zum Gebäude an. |
| Aktionen | Prozess starten | Startet einen Prozess nach Vorlage. |
| Aktionen | Merkmale erfassen | Checkliste der Mietspiegel-Merkmale des Gebäudes, siehe Reiter Mietspiegel. |
| Aktionen | Kopieren von Gebäude | Klassen und Merkmale eines anderen Gebäudes der Wirtschaftseinheit übernehmen. |
| Aktionen | Entwurf, Gesperrt | Statuswechsel, wie die Schaltflächen unten in der Maske. |
| Aktionen | Aktuelle Sicht übersetzen | Tryton-Standardfunktion zum Übersetzen der Bezeichnungen. |
| Verknüpfungen | E-Mail Archive, Objekt, Zählerableselisten | Öffnet zugehörige Datensätze in einem neuen Tab, wie bei der Wirtschaftseinheit. |
| Drucken | Datenblatt | Datenblatt des Gebäudes. |

## Grundstück

### Einstieg

![Menü und Liste Grundstück](einstieg-menue-grundstueck.png)

Menü **Immobilien › Stammdaten › Grundstück** öffnen. Die Liste zeigt die Grundstücke mit ihrer Wirtschaftseinheit; Filter und Reiter wie beim Gebäude.

### Anlegen und Bearbeiten

![Maske Grundstück mit Reiter Allg. Daten](maske-grundstueck.png)

Kopf und Reiter **Allg. Daten** wie beim Gebäude. Abweichend:

| Feld | Beschreibung |
|------|--------------|
| Typ | Hier **Grundstück**. |
| Übergeordnetes Objekt | Bei einem Grundstück immer eine Wirtschaftseinheit. |

Die Reiter **Bemessungen**, **Optionssatz**, **Partner**, **Unterobjekte** und **Aufgaben und Prozesse** sind aufgebaut wie bei der [Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}). Einen Reiter **Prüfungen** gibt es beim Grundstück nicht. Für Grundstücke gilt:

- **Partner:** Für Grundstücke ist keine Rolle ausgeliefert.
- **Unterobjekte:** Mietobjekte (z. B. Stellplätze) und Equipment (z. B. ein Spielplatz).

### Aktionen, Verknüpfungen und Drucken

| Menü | Eintrag | Bedeutung |
|------|---------|-----------|
| Aktionen | Neue Aufgabe | Legt eine Aufgabe zum Grundstück an. |
| Aktionen | Prozess starten | Startet einen Prozess nach Vorlage. |
| Aktionen | Entwurf, Gesperrt | Statuswechsel, wie die Schaltflächen unten in der Maske. |
| Aktionen | Aktuelle Sicht übersetzen | Tryton-Standardfunktion zum Übersetzen der Bezeichnungen. |
| Verknüpfungen | E-Mail Archive, Objekt, Zählerableselisten | Öffnet zugehörige Datensätze in einem neuen Tab, wie bei der Wirtschaftseinheit. |
| Drucken | Datenblatt | Datenblatt des Grundstücks. |
