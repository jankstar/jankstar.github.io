---
title: "5.5 Zähler und Messbelege"
description: "Zähler und Messbelege in Tryton: Zählerstände erfassen, Zählerableselisten, Eichfrist und Zählertausch für die Betriebskostenabrechnung."
weight: 50
---

Zähler sind [Equipment]({{< relref "/tryton-real-estate/stammdaten/equipment" >}}) mit dem Equipment Typ **Zähler**, z. B. Wasser-, Wärme-, Strom- oder Gaszähler. Sie hängen meist unter dem Mietobjekt, Hauptzähler unter Gebäude oder Wirtschaftseinheit. Jede Ablesung wird als **Messbeleg** gespeichert; die Messbelege sind Grundlage der verbrauchsabhängigen Umlage in der Betriebskostenabrechnung. Für die regelmäßige Ablesung vieler Zähler gibt es die **Zählerableselisten**.

## Zähler

Zähler werden wie jedes Equipment über **Immobilien › Stammdaten › Equipment** angelegt, siehe [5.4 Equipment und technische Anlagen]({{< relref "/tryton-real-estate/stammdaten/equipment" >}}). Im Reiter **Equipment** wird der Equipment Typ **Zähler** gewählt; dann erscheint der Reiter **Zähler**.

### Reiter Zähler

![Reiter Zähler mit Zähler Ablesungen](maske-reiter-zaehler.png)

| Feld | Beschreibung |
|------|--------------|
| Einheit | Maßeinheit der Zählerwerte, z. B. Kubikmeter oder kWh. |
| Faktor | Faktor, mit dem der Verbrauch multipliziert wird, z. B. bei einem Stromzähler mit Wandler. |
| kumulativer Zähler | Der Zählerstand steigt fortlaufend; der Verbrauch ist die Differenz zweier Stände. Ohne Kennzeichen misst der Zähler absolute Werte, z. B. einen Füllstand. |
| Ganzzahlige Werte | Zählerstände werden ohne Nachkommastellen erfasst. |
| Zähler ID | Nummer des eingebauten Zählers (Gerätenummer). Bei einem Zählertausch ändert sie sich, siehe Messbelege. |
| MeLo-ID / MaLo-ID | Messlokations-ID bzw. Marktlokations-ID, z. B. bei Strom- und Gaszählern. |
| letztes Datum der Ablesung, Benutzer letzte Ablesung | Datum und Ableser des letzten Messbelegs; werden berechnet. |
| Letzte Eichung | Datum der letzten Eichung bzw. des Einbaus eines geeichten Zählers. |
| Eichgültigkeit (Jahre) | Gültigkeit der Eichung in Jahren. |
| Eichfrist bis | Ende der Eichfrist; vorgeschlagen wird der 31.12. des Jahres der letzten Eichung plus Gültigkeit, überschreibbar. |
| Eichung fällig | Die Eichfrist endet innerhalb der nächsten 3 Monate oder ist abgelaufen. |
| letzter Messwert / letzter Verbrauch | Wert und Verbrauch des letzten Messbelegs; werden berechnet. |
| Zähler Ablesungen | Alle Messbelege des Zählers mit Ablesedatum, Zähler ID, Ablese-Typ, Wert, Einheit, Verbrauch und Ableser. Hier können auch neue Ablesungen erfasst werden. |

Die Schaltfläche **Verbrauch schätzen** öffnet einen Assistenten, der für ein Datum einen geschätzten Messbeleg anlegt. Liegen Ablesungen vor und nach dem Datum vor, wird zwischen ihnen interpoliert; sonst wird aus den letzten beiden Ablesungen innerhalb eines Jahres hochgerechnet. Anzugeben sind Zähler ID, Datum und Grund.

Mit der Aufgabenregel **Eichfrist läuft ab** entsteht 3 Monate vor Ablauf der Eichfrist eine Aufgabe zum Zählertausch bzw. zur Nacheichung, siehe [10 Aufgaben und Prozesse]({{< relref "/tryton-real-estate/aufgaben-prozesse" >}}).

## Messbelege

### Einstieg

![Menü und Liste Messbelege](messbelege.png)

Menü **Immobilien › Stammdaten › Messbelege** öffnen. Die Liste zeigt die Messbelege aller Zähler. Über die Felder **Gesellschaft**, **Wirtschaftseinheit**, **Übergeordnetes Objekt** und **Equipment** sowie **Von Datum** und **Bis Datum** wird die Liste eingeschränkt, z. B. auf die Zähler einer Wohnung in einem Abrechnungszeitraum. Neue Messbelege können direkt in der Liste mit **+** (Neu) erfasst werden.

### Felder

| Spalte | Beschreibung |
|--------|--------------|
| Ablesedatum | Datum des Zählerstands. |
| Zähler | Zähler (Equipment), zu dem der Messbeleg gehört. |
| Zähler ID | Nummer des abgelesenen Zählers. |
| Ablese-Typ | **initial (bei Installation)**, **Ablesung**, **geschätzt**, **lineare Interpolation** oder **Endablesung vor Ausbau**. |
| Wert | Abgelesener Zählerstand. |
| Einheit | Einheit des Zählers. |
| Verbrauch | Verbrauch seit dem vorherigen Messbeleg; wird berechnet. |
| Benutzer der Ablesung | Ableser. |

### Regeln beim Erfassen

- Der erste Messbeleg eines Zählers muss den Ablese-Typ **initial (bei Installation)** haben.
- Ablesungen und Schätzungen müssen dieselbe Zähler ID haben wie der vorherige Messbeleg.
- Bei einem kumulativen Zähler darf der Wert nicht kleiner sein als der vorherige.
- **Zählertausch:** Für den alten Zähler wird eine **Endablesung vor Ausbau** mit der alten Zähler ID erfasst, für den neuen Zähler am selben Tag ein Messbeleg **initial (bei Installation)** mit der neuen Zähler ID.
- Je Zähler, Datum und Zähler ID ist nur ein Messbeleg erlaubt.
- Bei **Ganzzahlige Werte** sind keine Nachkommastellen erlaubt.

Messbelege vom Typ **lineare Interpolation** legt die Betriebskostenabrechnung selbst an, wenn für Beginn oder Ende eines Abrechnungszeitraums ein Zählerstand zwischen zwei Ablesungen benötigt wird.

## Zählerableselisten

Eine Zählerableseliste sammelt alle Zähler unter einem Objekt für einen Ablesetermin. Sie wird ausgedruckt, beim Ablesen ausgefüllt und anschließend erfasst. Beim Abschließen legt das System die Messbelege an.

### Einstieg

![Menü und Liste Zählerableselisten](einstieg-ableselisten.png)

Menü **Immobilien › Stammdaten › Zählerableselisten** öffnen. Der Reiter **Entwurf** zeigt die offenen Listen mit Ablesedatum, Objekt, Ableser und Fortschritt (**Erfasst**: Zähler mit eingetragenem Stand / alle Zähler, z. B. 0 / 20 bei einer neuen Liste). Der Reiter **Alle** zeigt zusätzlich abgeschlossene und stornierte Listen mit ihrem Status.

- **Neu anlegen:** Schaltfläche **+** (Neu) in der Symbolleiste
- **Bearbeiten:** Zeile markieren und in die Maske wechseln

### Anlegen und Bearbeiten

![Maske Zählerableseliste – Messwerte erfassen](maske-ableseliste.png)

| Feld | Beschreibung |
|------|--------------|
| Objekt | Wirtschaftseinheit, Gebäude oder Mietobjekt; die Zähler darunter werden aufgelistet. Pflichtfeld. |
| Ablesedatum | Datum der Ablesung; Datum der erzeugten Messbelege. Pflichtfeld. |
| Anlagenart | Optional: nur Zähler dieser Anlagenart. |
| Namensfilter | Optional: nur Zähler, deren Name diesen Text enthält, z. B. „Wasser“ oder „Warmwasser“. |
| Ableser | Benutzer, der in den erzeugten Messbelegen als Ableser eingetragen wird. |
| Erfasst | Anzahl der Zähler mit eingetragenem Stand / Anzahl aller Zähler der Liste. |
| Status | **Entwurf**, **Abgeschlossen** oder **Storniert**. Im Entwurf ist die Liste bearbeitbar. |

Der Reiter **Zähler** enthält je Zähler eine Zeile; erfasst wird nur der **Neue Stand**:

| Spalte | Beschreibung |
|--------|--------------|
| Mieteinheit | Mietobjekt, unter dem der Zähler hängt. |
| Mieter | Hauptmieter des Mietobjekts am Ablesedatum (aus der Belegung). |
| Zähler | Zähler (Equipment). |
| Zählernummer | Zähler ID; vorgeschlagen aus dem letzten Messbeleg. |
| Letzte Ablesung / Letzter Stand | Datum und Wert des letzten Messbelegs vor dem Ablesedatum. |
| Neuer Stand | Abgelesener Zählerstand. |
| Einheit, Verbrauch | Einheit des Zählers und Verbrauch seit dem letzten Stand. |
| Bemerkung | Hinweis zur Ablesung, z. B. „Zähler nicht zugänglich“. |

Der neue Stand wird direkt in der Zeile eingetragen; der Verbrauch wird sofort berechnet (im Bild beim ersten Zähler 47 − 25 = 22 m³). Bei vielen Spalten ist die Tabelle seitlich verschiebbar. Über **≡** lassen sich zusätzlich die Spalten **Zählerablesung** (erzeugter Messbeleg) und **Ablesung erzeugt** einblenden.

Der Reiter **Weitere Informationen** enthält Wirtschaftseinheit und Gesellschaft (werden gesetzt), **Abgeschlossen von**, **Abgeschlossen am** und **Notizen**.

### Ablauf

1. Liste anlegen: Objekt, Ablesedatum und bei Bedarf Anlagenart oder Namensfilter wählen.
2. **Zähler laden:** Die Zähler unter dem Objekt werden mit Mieteinheit, Mieter und letztem Stand eingetragen. Erneutes Laden ergänzt neue Zähler und aktualisiert die Angaben; eingetragene Stände bleiben erhalten.
3. Liste über **Drucken › Ableseliste (de)** ausdrucken und ablesen.
4. Neue Stände eintragen.
5. **Abschließen:** Für jede Zeile mit neuem Stand wird ein Messbeleg vom Typ **Ablesung** angelegt. Gibt es zum Zähler am Ablesedatum schon einen Messbeleg, wird dieser verknüpft. Fehlen Stände, erscheint eine Warnung, die bestätigt werden kann.
6. **Folgeliste:** Legt die Liste für den nächsten Termin an – gleiches Objekt und gleiche Filter, Ablesedatum ein Jahr später – und öffnet sie.

**Auf Entwurf zurücksetzen** löscht die Messbelege, die die Liste angelegt hat; verknüpfte, schon vorhandene Messbelege bleiben erhalten.

### Aktionen, Verknüpfungen und Drucken

| Menü | Eintrag | Bedeutung |
|------|---------|-----------|
| Aktionen | Zähler laden, Abschließen, Stornieren | Nur im Status Entwurf, siehe Ablauf. |
| Aktionen | Auf Entwurf zurücksetzen | Bei abgeschlossener oder stornierter Liste, wie die Schaltfläche unten in der Maske. |
| Aktionen | Folgeliste | Nur bei abgeschlossener Liste, siehe Ablauf. |
| Aktionen | Aktuelle Sicht übersetzen | Tryton-Standardfunktion zum Übersetzen der Bezeichnungen. |
| Verknüpfungen | E-Mail Archive | E-Mails zur Ableseliste. |
| Drucken | Ableseliste (de) | Ableseliste zum Ausfüllen beim Ablesen. |

Von jedem Objekt aus öffnet **Verknüpfungen › Zählerableselisten** die Ableselisten dieses Objekts.
