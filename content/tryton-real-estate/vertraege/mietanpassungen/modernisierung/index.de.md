---
title: "6.7.4 Modernisierung"
description: "Mieterhöhung nach Modernisierung (§§ 555b–559e BGB) in Tryton: Maßnahme, Kosten, Umlage, Ankündigung, Härteeinwand und Anpassungslauf."
weight: 40
---

Nach einer Modernisierung darf der Vermieter die Jahresmiete um einen Teil der Kosten erhöhen (§§ 555b–555f, 559–559e BGB). Anders als bei Index- und Vergleichsmiete ist der Ausgangspunkt keine einzelne Kondition, sondern eine **Maßnahme** an Gebäuden einer Wirtschaftseinheit, deren Kosten auf mehrere Mietobjekte verteilt werden. Die Modernisierung hat deshalb zwei Phasen:

1. die **Maßnahme** – Planung, Ankündigung, Arbeiten, Kosten;
2. die **Mieterhöhung** per [Anpassungslauf]({{< relref "/tryton-real-estate/vertraege/mietanpassungen#anpassungslauf" >}}) – Erhöhungserklärung und Teilung der Miet-Kondition.

Beide Phasen begleitet ein Prozess mit Aufgaben und Fristen.

## Voraussetzungen (einmalig)

- **Modernisierungsregel** unter **Immobilien › Einstellungen › Modernisierungsregeln** (nur Gruppe Immobilienverwaltung). Die gesetzlichen Werte ab 01.01.2024 werden mitgeliefert:
  - Umlagesatz 8 %, bei Heizung 10 %;
  - vereinfachtes Verfahren bis 10.000 € je Wohnung, mit 30 % Erhaltungspauschale (Heizung 15 %) und 5 Jahren Sperre;
  - Kappung 3,00 €/m², bzw. 2,00 €/m² bei einer Miete unter 7,00 €/m², in 6 Jahren; Heizung zusätzlich 0,50 €/m²;
  - Ankündigungsfrist 3 Monate, Verlängerung 6 Monate, Toleranz 10 %.

  Es gilt die Regel, die am Ankündigungsdatum gültig ist (sonst am geplanten Beginn).

  {{% callout style="warning" title="Kappungs-Bemessung setzen" %}}
  In der Regel einmal die **Kappungs-Bemessung** setzen, z. B. Wohnfläche. Sie liefert die Fläche für die Kappung und die Erhöhung je m² im Schreiben. Ohne sie ist **Ankündigen** nicht möglich (M02).
  {{% /callout %}}
- **Verfahren zulassen:** Modernisierung an der Vertragsart und an der Konditionsart der Miete, siehe [2.5.4 Verträge]({{< relref "/tryton-real-estate/system/einstellungen/vertraege" >}}). Nur eine solche Kondition wird zur Miet-Kondition einer Wohnung.
- **Bemessungen pflegen:** die Bemessung für den Verteilerschlüssel an den Wohnungen, meist Wohnfläche.
- Für die Kalkulation (optional): Mietspiegel an der Wirtschaftseinheit und die Einstufung der Objekte, siehe [6.7.3 Vergleichsmiete]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/vergleichsmiete" >}}).

## Maßnahme anlegen und planen

Menü **Verträge › Mietanpassungen › Modernisierungen** mit den Reitern Planung, In Arbeit, Fertiggestellt, Abgerechnet und Alle; **Neu** legt eine Maßnahme an (Nummer MOD-Jahr-Nr.). Der Prozess **Modernisierung** startet automatisch und steht im Reiter **Ablauf**.

1. Reiter **Maßnahme**:
   - Name, Gesellschaft, Wirtschaftseinheit, Gebäude (leer = alle Gebäude der Wirtschaftseinheit);
   - **Maßnahmenarten** nach § 555b BGB: Nr. 1 energetisch, 1a Heizungsanlage, 2 Primärenergie/Klimaschutz, 3 Wasser, 4 Gebrauchswert, 4a Glasfaser, 5 Wohnverhältnisse, 6 nicht zu vertreten, 7 neuer Wohnraum – Nr. 2 und 7 allein erlauben keine Erhöhung;
   - **Variante:** Standard (§ 559, 8 %), Vereinfacht (§ 559c) oder Heizungsanlage (§ 559e, 10 %);
   - Beschreibung und Energieeinsparung (Pauschalwerte zulässig);
   - Kennzeichen **Bagatelle** (§ 555c Abs. 4, mit Begründung), **Leerstand**, **Umfassende Modernisierung** (§ 556f, mit Begründung), **Nicht vom Vermieter zu vertreten** und **JAZ Wärmepumpe nachgewiesen**.
2. Reiter **Termine:** geplanter Beginn und Dauer, später tatsächlicher Beginn und Ende.
3. Reiter **Kosten:**
   - Kostenzeilen **je Gewerk**, nicht je Rechnung, z. B. Fassade, Fenster, Heizung: Art Plan, später Ist; netto, Steuer, brutto; Maßnahmenart; **Erhaltungsanteil** (Kein, Prozent oder Betrag netto – nur bei der Variante Standard, sonst gilt die Pauschale) und optional ein einzelnes Mietobjekt, z. B. für einen Balkon.
   - **Voraussichtliche Kosten** und **voraussichtliche Betriebskosten**; beides gehört in die Ankündigung.
4. Reiter **Drittmittel** (§ 559a): Zuschuss oder Leistung Dritter mindern die Kosten, der Zinsvorteil eines Darlehens mindert den Jahresbetrag; optional für ein einzelnes Mietobjekt.
5. **Verteilerschlüssel:** Gleiche Anteile, Bemessung (Standard, mit wählbarer Bemessungsart, z. B. Wohnfläche oder Anzahl Fenster) oder Manuell (Menge je Mietobjekt, Begründung Pflicht). Der Anteil ist Menge / Summe der teilnehmenden Mietobjekte.
6. **Mietobjekte ermitteln:** Das Modul übernimmt die Wohnungen der Gebäude mit Vertrag, Miet-Kondition und Menge; ein erneuter Aufruf aktualisiert sie und behält manuelle Einstellungen. Im Reiter **Mietobjekte** kannst du je Mietobjekt korrigieren:
   - **In Verteilung:** abwählen nur mit Grund – der Anteil der anderen steigt; Leerstand nimmt immer teil, den Anteil trägt der Eigentümer (M09);
   - **Vorsteuerabzug:** Kosten netto statt brutto; vorgeschlagen, wenn eine Kondition des Vertrags Umsatzsteuer trägt;
   - **Keine Erhöhung** mit Grund (Leerstand, Gewerbe, Staffel-/Indexmiete, Härte anerkannt, Kündigung, Verzicht …): Das Mietobjekt bleibt in der Verteilung, bekommt aber keine Erhöhung und kein Schreiben. Leerstand und Gewerbe werden automatisch gesetzt.

   Je Mietobjekt siehst du Anteil, umlagefähige Kosten und die **voraussichtliche Erhöhung** je Monat nach Kappung.
7. **Kalkulation** (optional, Reiter **Kalkulation**, in Planung und Entwurf): **Geplante Merkmale** je Gebäude und Wohnung erfassen – Mietspiegel-Klassen oder -Merkmale (Hinzufügen/Entfernen) und Bemessungen nach der Modernisierung, z. B. den neuen Energieverbrauchskennwert. **Kalkulieren** zeigt je Wohnung:
   - Erhöhung nach § 559 und die Miete danach;
   - Vergleichsmiete vorher und nachher sowie die Miete nach § 558 und ab wann sie verlangt werden kann;
   - Grenze bei Neuvermietung;
   - Mehrmiete p. a. und Rendite.

   Damit entscheidest du, ob die Umlage nach § 559 oder eine spätere Vergleichsmiete im modernisierten Zustand (§ 558) mehr bringt.
8. **Planung übernehmen:** Status Planung → Entwurf.

### Berechnung der Umlage

1. Anteil je teilnehmendem Mietobjekt nach dem Schlüssel; Kosten und Drittmittel eines einzelnen Mietobjekts gehen nur an dieses.
2. Kostenbasis netto oder brutto (Vorsteuerabzug).
3. Umlagefähige Kosten = Kosten − Erhaltungsanteil (Prozent, Betrag oder Pauschale 30 % / 15 %) − Zuschüsse und Leistungen Dritter.
4. Jahresbetrag = umlagefähige Kosten × 8 % bzw. 10 % − Zinsvorteil; Monatsbetrag = Jahresbetrag / 12.
5. Kappung: Fläche × 3,00 €/m² (2,00 €/m² bei einer Miete unter 7,00 €/m²), bei Heizung zusätzlich Fläche × 0,50 €/m²; Vorerhöhungen der letzten 6 Jahre rechnet der Anpassungslauf an.

Beispiel: Gebäude mit 420 m², Kosten 180.000 €, Erhaltungsanteil 25 %, Zuschuss 15.000 € → umlagefähig 120.000 €; eine Wohnung mit 64,20 m² trägt 18.342,86 € × 8 % = 1.467,43 € im Jahr, also **122,29 € im Monat**.

## Ankündigung (§ 555c BGB)

1. Optional **Probeschreiben:** die Ankündigung des ersten Mietobjekts mit Vermerk ENTWURF, ohne Archivierung.
2. **Ankündigen.** Das Modul prüft:
   - M01: mindestens eine Maßnahmenart mit Erhöhung;
   - M02: Pflichtangaben – Beschreibung, geplanter Beginn und Dauer, voraussichtliche Kosten und Betriebskosten, Kappungs-Bemessung;
   - M03: Beginn frühestens 3 Monate nach der Ankündigung;
   - M04: Heizung nur mit Nr. 1a;
   - M05: Grenze des vereinfachten Verfahrens (Warnung);
   - M10: Mengen beim Schlüssel Bemessung oder Manuell.

   Danach entsteht je Mietobjekt mit Erhöhung ein Schreiben an alle Hauptmieter (Bericht **Modernisierungsankündigung**) mit Art und Umfang, Energieeinsparung, Beginn und Dauer, der voraussichtlichen Erhöhung je Monat und je m², den künftigen Betriebskosten, dem Hinweis auf den Härteeinwand mit Formular und Frist (§ 555d) und dem Sonderkündigungsrecht (§ 555e). Die Schreiben werden am Mietobjekt und am Vertrag archiviert, die angekündigte Erhöhung wird eingefroren.
3. **Zugang erfassen**, gesammelt oder je Mietobjekt. Daraus berechnet das Modul die Fristen **Härteeinwand bis** und **Sonderkündigung bis**: Ende des Monats nach Zugang. Am Mietobjekt erfasst du außerdem **Ankündigung mangelhaft**, **Härtehinweis fehlt** (dann keine Härtefrist), den Härteeinwand mit Datum, Notiz und Entscheidung und **Gekündigt**.

Ausnahmen: Bagatellmaßnahmen (§ 555c Abs. 4) und Leerstand werden nicht angekündigt – die Arbeiten beginnen direkt.

## Durchführung

1. **Arbeiten beginnen.** M06 warnt, wenn der Beginn weniger als 3 Monate nach dem Zugang der Ankündigung liegt.
2. Im Laufe der Arbeiten **Ist-Kosten** erfassen, am besten mit Bezug auf die Lieferanten-Rechnungszeile; netto und Steuer werden übernommen. An der Rechnungszeile ist die Maßnahme dann sichtbar und suchbar.
3. **Fertigstellen.** M07 prüft, dass Kosten vorhanden sind und bei der Variante Standard jede Zeile einen Erhaltungsanteil hat. Der Reiter **Prüfmeldungen** zeigt ab dann M08: die Vorsteuer der Mietobjekte im Vergleich zur Optionsquote der Wirtschaftseinheit (§ 15a UStG, Steuerberater hinzuziehen); ohne Abweichung steht dort „Keine Meldungen.“
4. Bei Mietspiegel-Nutzung **Geplante Merkmale übernehmen:** Die geplanten Klassen, Merkmale und Bemessungen gelten an den Objekten ab der Fertigstellung (Prozessschritt 90).

Eine Leerstandsmaßnahme ist mit dem Fertigstellen sofort abgerechnet.

## Mieterhöhung (§§ 559, 559b BGB)

1. In der fertiggestellten Maßnahme **Anpassungslauf anlegen.** Der Lauf öffnet sich mit der Maßnahme als Parameter; Stichtag und Erklärungsdatum sind heute, frühestens die Fertigstellung.
2. **Selektieren:** je Mietobjekt ein Vorgang. Ausgelassen wird mit Grund im Protokoll: Keine Erhöhung, kein Vertrag bzw. keine Miet-Kondition, schon ein Vorgang für das Mietobjekt, Verfahren nicht zulässig.
3. **Berechnen:**
   - Erhöhung = umlagefähige Kosten × 8 % / 10 %, abzüglich Zinsvorteil; gerechnet wird mit den Ist-Kosten, solange keine da sind mit den Plan-Kosten;
   - Kappung mit den Vorerhöhungen der Wohnung aus den letzten 6 Jahren (Heizung getrennt);
   - Wirksamkeit ab Beginn des 3. Monats nach Zugang, **+ 6 Monate** ohne Ankündigung bzw. ohne deren Zugang, bei mangelhafter Ankündigung oder bei mehr als 10 % über der angekündigten Erhöhung.

   Die Werte stehen am Vorgang im Reiter **Modernisierung**. Prüfungen E01–E14, z. B. Staffelmiete (E02), Indexmiete nur bei nicht zu vertretenden Umständen (E03), Härteeinwand offen (E04), Kappung greift (E05), Verschiebung um 6 Monate (E07), keine Kappungsfläche (E10), Kündigung (E11), Erklärung vor Fertigstellung (E12).
4. **Freigeben**, optional **Probeschreiben**, dann **Ankündigen.** Die Erhöhungserklärung (Bericht **Modernisierungs-Mieterhöhung**) enthält Bezug auf die Ankündigung, Kosten je Gewerk, Erhaltungsanteil, Drittmittel, Schlüssel und Anteil, umlagefähige Kosten, Jahres- und Monatsbetrag, alte und neue Miete, Wirksamkeit, Kappung bzw. Verlängerung und das Sonderkündigungsrecht nach § 561. **Schreiben drucken** liefert alle noch zu versendenden Schreiben.
5. Im Reiter **Zugang / Zustimmung** den Zugang erfassen; das Modul berechnet **Sonderkündigung bis** (Ende des zweiten Monats nach Zugang, § 561). Eine Zustimmung des Mieters ist nicht nötig, die Erhöhung ist eine einseitige Erklärung. Hat der Mieter nach § 561 gekündigt, setzt du **Gekündigt** und stornierst den Vorgang.
6. **Ausführen** (im Hintergrund): Die Kondition wird zum Wirksamkeitsdatum mit der neuen Miete geteilt, Fehler werden ins Protokoll geschrieben. Ist der Lauf abgeschlossen, wechselt die Maßnahme auf **Abgerechnet**.

Am Mietobjekt der Maßnahme sind danach Vorgang und Status der Erhöhung zu sehen.

## Prozess und Fristen

| Nr. | Prozessschritt | Erledigt durch |
|-----|----------------|----------------|
| 10 | Planen und kalkulieren (optional) | Status Entwurf |
| 20 | Mietobjekte ermitteln | Mietobjekte vorhanden |
| 30 | Maßnahme ankündigen | Status Angekündigt; nicht bei Leerstand und Bagatelle |
| 40 | Zugang der Ankündigung erfassen | Zugang an jedem Mietobjekt mit Erhöhung |
| 50 | Härteeinwände und Sonderkündigungen bearbeiten | Fristen abgelaufen, kein Einwand offen |
| 60 | Arbeiten beginnen | Status In Arbeit |
| 70 | Arbeiten fertigstellen, Kosten ermitteln | Status Fertiggestellt |
| 80 | Anpassungslauf starten | Maßnahme abgerechnet; nicht bei Leerstand |
| 90 | Mietspiegelmerkmale aktualisieren | manuell; nur mit Mietspiegel |

Fristaufgaben: Härteeinwand (je Mietobjekt), Beginn der Arbeiten und Erhöhungserklärung (an der Maßnahme) sowie Sonderkündigung nach § 561 (am Vorgang), siehe [2.5.7 Aufgaben und Prozesse]({{< relref "/tryton-real-estate/system/einstellungen/aufgaben-prozesse" >}}). Der Anpassungslauf hat seinen eigenen Prozess **Anpassungslauf Modernisierung**.

## Weitere Unterstützung

- **Leerstand:** Im Prozess **Leerstand / Neuvermietung** des Mietobjekts legt der optionale Schritt **Modernisierung planen** eine Leerstandsmaßnahme an (Assistent **Modernisierung planen (Leerstand)**), siehe [5.3 Mietobjekte]({{< relref "/tryton-real-estate/stammdaten/mietobjekt" >}}). Sie wird ohne Ankündigung durchgeführt und bei Fertigstellung abgerechnet. Ihr Betrag zählt bei der nächsten Vermietung.
- **Neuvermietung:** Der Assistent **Prüfung Vergleichsmiete Neuvermietung** erhöht die Grenze der Mietpreisbremse um den Modernisierungsbetrag der letzten 3 Jahre (§ 556e Abs. 2). Nach einer umfassenden Modernisierung gilt bei der ersten Vermietung keine Grenze (§ 556f).

{{% callout style="warning" title="Nicht abgedeckt" %}}
- Gewerbemietverträge – dort gilt nur der Vertrag, ggf. mit einer freien Anpassung.
- Die rechtliche Prüfung der Schreibvorlagen.
- Bei Heizungsanlagen mit Wärmepumpe ist die 50-%-Regel manuell zu prüfen (E08).
{{% /callout %}}
