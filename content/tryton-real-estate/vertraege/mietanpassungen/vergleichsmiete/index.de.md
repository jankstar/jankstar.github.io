---
title: "6.7.3 Vergleichsmiete"
weight: 30
---

Eine Mieterhöhung auf die ortsübliche Vergleichsmiete (§§ 558–558b BGB) begründet das Modul ausschließlich über einen im System hinterlegten **Mietspiegel**. Gutachten, Vergleichswohnungen oder eine Mietdatenbank werden nicht unterstützt. Die Arbeit hat drei Teile: Mietspiegel und Stammdaten einmal pflegen, die Objekte einstufen und dann je Erhöhung einen [Anpassungslauf]({{< relref "/tryton-real-estate/vertraege/mietanpassungen#anpassungslauf" >}}) durchführen.

## Stammdaten (einmalig)

Mietspiegel werden unter **Immobilien › Einstellungen › Mietspiegel** gepflegt (Gruppe Immobilien-Administration).

### Version und Reiter Allgemein

![Mietspiegel mit Version, Reiter Allgemein](mietspiegel-version.png)

Der Mietspiegel hat **Name**, **Geltungsbereich**, **Unternehmen** und die **Aktuelle Version**; darunter stehen die **Versionen** mit Name, **Gültig ab** und **Gültig bis**. Jede Version hat die Reiter **Allgemein**, **Klassifizierungsmerkmale**, **Tabellenfelder**, **Merkmalgruppen** und **Prüfergebnis** sowie die Schaltflächen **Prüfen**, **Importieren** und **Neue Version**.

Im Reiter **Allgemein** der Version:

- **Art:** einfach oder qualifiziert;
- **Methode:** Tabelle (wie in Berlin) oder Regression;
- **Erhebungsstichtag** des Mietspiegels;
- **Bemessung Wohnfläche** (Standard Wohnfläche) und bei Bedarf **Weitere Bemessungen**, z. B. Anzahl Räume;
- **Gruppenwirkung (%)** – Spannenanteil der Merkmalgruppen (Standard 20 %) – und **Gruppen saldieren** (positive und negative Gruppen werden verrechnet);
- **Rundung (Stellen)**;
- **Amtliches Dokument** (Quelle) und **Mietbegriff** (welche Miete der Mietspiegel ausweist und wofür er gilt); das amtliche Dokument selbst wird als Anhang an die Version gehängt;
- **Gesperrt** zeigt an, dass angenommene Berechnungen die Version verwenden; sie lässt sich dann nicht mehr ändern.

### Klassifizierungsmerkmale

![Mietspiegel-Version, Reiter Klassifizierungsmerkmale](mietspiegel-klassifizierung.png)

Die Klassifizierungsmerkmale sind die Tabellenachsen mit ihren Klassen. Je Merkmal werden **Code**, **Name**, die **Ebene** (Wirtschaftseinheit / Gebäude / Mietobjekt) und die **Herkunft** festgelegt: manuell, Baujahr des Gebäudes oder Bemessung mit Wertebereich je Klasse. Im Berliner Beispiel sind das die **Wohnlage** (Wirtschaftseinheit, manuell) und die **Bezugsfertigkeit** (Gebäude, aus dem Baujahr).

### Tabellenfelder

![Mietspiegel-Version, Reiter Tabellenfelder](mietspiegel-tabellenfelder.png)

Jedes Tabellenfeld hat einen **Code**, je Klassifizierungsmerkmal eine Klasse (Spalte **Klassen**, z. B. „mittel, 1986 bis 1990 West“), den Flächenbereich **Fläche ab** / **Fläche bis unter** (leer = offen), **Unterwert**, **Mittelwert** und **Oberwert** in €/m² sowie das Kennzeichen **qualifiziert**. Der Berliner Mietspiegel 2026 hat 189 Tabellenfelder. Bei der Berechnung wird das Feld gesucht, dessen Klassen und Flächenbereich zur Wohnung passen; Ausgangspunkt ist der Mittelwert, die Merkmalgruppen verschieben ihn innerhalb der Spanne.

### Merkmalgruppen und Merkmale

![Mietspiegel-Version, Reiter Merkmalgruppen](mietspiegel-merkmalgruppen.png)

Jede Merkmalgruppe hat **Code**, **Name**, **Regel** und **Reihenfolge**, darunter ihre **Merkmale** mit Code, Name, **Richtung** (wohnwerterhöhend + / wohnwertmindernd −), **Wirkung** (Stimme bei der Regel Mehrheit, sonst Betrag), **Wert** und **Ebene**. Im Bild die Gruppe **Küche** des Berliner Mietspiegels mit 14 Merkmalen, z. B. „Küche ohne Fenster und Entlüftung“ (−) oder „Hochwertiger Bodenbelag (Küche)“ (+). Für die Gruppe gilt:

- Regel Mehrheit (+1/0/−1) oder additiv (€/m² bzw. %);
- je Merkmal Richtung, Ebene und „nur für Klassen“ (z. B. nur bis Baujahr 2001);
- automatisches Setzen über eine Bemessung (z. B. Energieverbrauchswert);
- gegenseitige Ausschlüsse.

### Importieren, Prüfen, Neue Version

Tabellenfelder und Merkmale lassen sich mit **Importieren** (CSV) mit Vorschau und Problemliste einlesen. Danach mit **Prüfen** die Konsistenz kontrollieren; das Ergebnis steht im Reiter **Prüfergebnis**. Für einen neuen Mietspiegel kopiert **Neue Version** alles; die Codes bleiben gleich und damit auch die Einstufungen der Objekte.

### Weitere Stammdaten

- **Nutzungsart Wohnung:** Sie muss die Vergleichsmiete zulassen (Standard). Nur dann gilt ein Objekt als Wohnung mit Mietspiegeldaten; Stellplätze und Gewerbe sind ausgeschlossen.
- **Vertragsart und Konditionsart der Miete** (z. B. 1000): Die Vergleichsmiete muss unter **Zulässige Anpassungsverfahren** stehen. Die Miet-Kondition muss monatlich sein und darf keine Betriebskosten enthalten (V11).
- Optional die Aufgabenregeln für Zustimmungsfrist und Klagefrist, z. B. M09 „Zustimmungsfrist abgelaufen“, siehe [2.4.7 Aufgaben und Prozesse]({{< relref "/tryton-real-estate/system/einstellungen/aufgaben-prozesse" >}}).

## Objekte einstufen

Die Einstufung erfolgt im Reiter **Mietspiegel** der Objekte, siehe [5.1 Wirtschaftseinheit]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}). Klassen auf einer tieferen Ebene überschreiben die Klassen darüber; Merkmale addieren sich über alle drei Ebenen.

### Wirtschaftseinheit

![Wirtschaftseinheit, Reiter Mietspiegel mit Klasse Wohnlage](einstufung-wirtschaftseinheit.png)

Der Reiter **Mietspiegel › Klassen und Merkmale** zeigt oben den zugeordneten **Mietspiegel** und die Schaltflächen **Merkmale erfassen**, **Matrix exportieren** und **Matrix importieren**, darunter die Liste **Klassen und Merkmale zum Mietspiegel**. Jeder Eintrag hat die **Art** (Klasse oder Merkmal), das Klassifizierungsmerkmal bzw. Merkmal, die **Klasse**, **Gültig ab** / **Gültig bis** und den **Nachweis**; im Bild die Wohnlage „mittel“ der Wirtschaftseinheit. Im Feld **Daten zum Mietspiegel** stehen Bemerkungen und Begründungen, z. B. die Fundstelle im Straßenverzeichnis.

- **Mietspiegel** zuordnen. Erst damit erscheint der Reiter auch an Gebäuden und Wohnungen.
- **Klassen** auf Ebene Wirtschaftseinheit. In Berlin ist das die Wohnlage einfach/mittel/gut, manuell laut Straßenverzeichnis; der Nachweis wird unter **Daten zum Mietspiegel** eingetragen.
- **Merkmale des Wohnumfelds** (Gruppe 5), soweit sie auf dieser Ebene liegen.
- **Kappungsgrenze 15 % (Verordnung)** mit Gültigkeit, wenn die Landesverordnung gilt, sonst 20 %. Dazu **Angespannter Wohnungsmarkt**, wichtig für die Prüfung bei Neuvermietung.
- Mit **Matrix exportieren** / **Matrix importieren** lassen sich alle Gebäude und Wohnungen der Wirtschaftseinheit in einer Tabellenkalkulation pflegen – eine Zeile je Objekt, eine Spalte je Klasse oder Merkmal.

![Dialog Mietspiegel-Matrix exportieren](matrix-exportieren.png)

**Matrix exportieren** fragt nach **Zeilen** (Wohnungen oder Gebäude) und **Stichtag** und liefert eine CSV-Datei (Trennzeichen Semikolon): je Objekt eine Zeile, je Klassifizierungsmerkmal eine Spalte mit dem Klassencode und je Merkmal der Ebene eine Spalte, in der ein „x“ das zugeordnete Merkmal kennzeichnet. Die zweite Zeile beginnt mit „#“ und enthält die Namen. So sind alle Wohnungen × Merkmale auf einen Blick zu sehen und in der Tabellenkalkulation zu pflegen. **Matrix importieren** liest die geänderte Datei ein, zeigt in einer **Vorschau** die Änderungen und Probleme und übernimmt sie mit **Übernehmen** – nur, wenn keine Probleme gemeldet sind. Als Markierung gelten x, 1, ja/j, yes/y, true/wahr; eine leere Zelle entfernt das Merkmal.

### Gebäude

![Gebäude, Reiter Mietspiegel](einstufung-gebaeude.png)

Am Gebäude gibt es kein Feld Mietspiegel; er kommt von der Wirtschaftseinheit. Der Reiter zeigt die Schaltflächen **Merkmale erfassen** und **Kopieren von Gebäude**, die Liste der Klassen und Merkmale mit Art (+ wohnwerterhöhend, − wohnwertmindernd), Klasse, Merkmal, Gültig ab/bis und Nachweis sowie das Feld **Daten zum Mietspiegel**. Im Bild sind drei wohnwerterhöhende Merkmale erfasst, z. B. abschließbarer Fahrradabstellraum und besonders ruhige Lage.

![Gebäude, Reiter Bemessungen](einstufung-gebaeude-bemessungen.png)

Den Energieverbrauchskennwert erfasst man im Reiter **Bemessungen** des Gebäudes mit **von** (Gültig ab), Wert und Einheit kWh/(m²a), im Bild neben der Bruttogeschossfläche.

- **Baujahr** (Bezugsfertigkeit): Daraus wird die Altersklasse abgeleitet, in Berlin 12 Klassen. Klassen, die vom Bezirk abhängen, z. B. 1973–1990 Ost/West, sind „nur manuell“ und müssen zugeordnet werden.
- Bemessung **Energieverbrauchskennwert** (kWh/(m²a)), bereits umgerechnet. Daraus werden die Energiemerkmale automatisch gesetzt; B09 warnt, wenn der Wert fehlt.
- **Gebäudemerkmale** (Gruppe 4, z. B. Aufzug, Wärmedämmung, Zustand Treppenhaus) mit Gültigkeit und Nachweis – schnell über **Merkmale erfassen** (Checkliste) oder **Kopieren von Gebäude**.

### Wohnung

![Wohnung, Reiter Mietspiegel](einstufung-wohnung.png)

An der Wohnung hat der Reiter **Mietspiegel** die Unterreiter **Klassen und Merkmale** und **Berechnung**. Neben **Merkmale erfassen** gibt es hier **Kopieren von Mietobjekt** und **Vergleichsmiete berechnen**. Im Bild sind sechs Wohnungsmerkmale erfasst, darunter fünf wohnwerterhöhende (z. B. Einbauküche, großer Balkon) und ein wohnwertminderndes (− Geschirrspüler nicht stellbar).

![Dialog Mietspiegel-Merkmale erfassen](merkmale-erfassen.png)

**Merkmale erfassen** öffnet den Dialog **Mietspiegel-Merkmale erfassen** mit Objekt, Mietspiegel-Version und Stichtag. Die Liste zeigt die Merkmale des Objekts mit Code, Name, Richtung, Wirkung (Stimme bzw. Wert), Wert und Ebene; mit **+** und der Suche kommen weitere Merkmale der Ebene hinzu, mit **−** werden sie entfernt. **Speichern** übernimmt die Liste in den Reiter. Der Dialog ist an Gebäude und Wohnung gleich.

- Bemessung **Wohnfläche** zum Stichtag. Sie bestimmt das Tabellenfeld (Flächenbereich) und ergibt: Vergleichsmiete = €/m² × Wohnfläche.
- **Wohnungsmerkmale** der Gruppen Bad/WC, Küche und Wohnung, z. B. Einbauküche, Parkett oder Bad ohne Fenster – über **Merkmale erfassen** oder **Kopieren von Mietobjekt**, das bei gleich ausgestatteten Wohnungen Zeit spart.
- **Vergleichsmiete berechnen** (Aktionen am Mietobjekt) zeigt das Ergebnis für die Wohnung direkt, ohne Lauf.

## Mietspiegel-Berechnung

Je Wohnung und Stichtag gibt es genau eine offene Berechnung, zu finden unter **Verträge › Mietanpassungen › Mietspiegel-Berechnungen** oder im Reiter **Mietspiegel** der Wohnung.

![Wohnung, Reiter Mietspiegel › Berechnung](berechnung-wohnung.png)

Der Unterreiter **Berechnung** der Wohnung listet die Mietspiegel-Berechnungen mit Stichtag, Version und Status. Der Reiter **Ergebnis** zeigt Tabellenfeld, Unter-, Mittel- und Oberwert, Wohnfläche, **Saldo Gruppen**, **Miete je m²**, **Vergleichsmiete** und **Prüfen** mit den Prüfmeldungen, darunter das **Protokoll** der Einordnung. Weitere Reiter sind **Freigabe**, **Eingaben** und **Bezüge**; **Neu berechnen** rechnet erneut. Im Bild: Feld 73 mit 6,66 / 8,27 / 11,73 €/m², Saldo +3, also 8,27 + 3 × 20 % × (11,73 − 8,27) = 10,35 €/m² und 10,35 €/m² × 83,00 m² = 859,05 €.

1. **Berechnen:** Version zum Stichtag, Wohnfläche, Klassen (manuell oder abgeleitet), passendes Tabellenfeld, gültige und automatische Merkmale, dann die Spanneneinordnung je Gruppe. Ergebnis ist ein Protokoll mit Schnappschuss aller Eingaben. Prüfungen:
   - B01–B04 sind Fehler (z. B. keine Klasse, kein oder mehrere Tabellenfelder);
   - B05 (außerhalb des qualifizierten Bereichs), B06 (sich ausschließende Merkmale), B07 und B09 sind Warnungen.
2. **Freigeben** (Annehmen) ist Pflicht: Die €/m² werden übernommen, auf Wunsch abweichend; bei einer Warnung oder einem abweichenden Wert ist eine Begründung nötig. Angenommene Berechnungen sind Beweismittel: Sie lassen sich nicht löschen und sperren die Mietspiegelversion.

## Mieterhöhung per Anpassungslauf

![Anpassungslauf Vergleichsmiete, Reiter Ablauf](lauf-ablauf.png)

Mit dem ersten Selektieren startet der Prozess **Anpassungslauf Vergleichsmiete** mit sechs Schritten: 10 Parameter prüfen und selektieren, 20 Berechnungen freigeben und berechnen, 30 Freigeben, 40 Zustimmung verlangen (Schreiben), 50 Zugänge und Zustimmungen erfassen, 60 Ausführen. Jeder Schritt hat eine Fälligkeit und wird mit dem Status des Laufs automatisch erledigt. Die Schaltfläche **Berechnungen** unten öffnet die Mietspiegel-Berechnungen des Laufs. Im Bild ist der Lauf **Abgeschlossen**, alle Schritte sind erledigt.

Menü **Verträge › Mietanpassungen › Anpassungsläufe**, **Neu**, Verfahren **Vergleichsmiete**:

1. **Parameter:**
   - Gesellschaft, Wirtschaftseinheit, optional Verträge;
   - Stichtag und geplantes Erklärungsdatum;
   - **Mindesterhöhung** in € und %;
   - **Berechnungen anlegen** (Standard ja).

   ![Anpassungslauf Vergleichsmiete, Reiter Parameter](lauf-parameter.png)

   Im Reiter **Parameter** stehen Mindesterhöhung in € und %, **Berechnungen anlegen** und die Filter **Wirtschaftseinheiten** und **Verträge**; bleibt die Vertragsliste leer, werden alle passenden Verträge der Wirtschaftseinheiten selektiert. Stichtag und Erklärungsdatum stehen im Kopf des Laufs.
2. **Selektieren:** Ausgewählt werden laufende Verträge mit monatlicher Miet-Kondition für genau eine Wohnung. Ausgelassen wird mit Protokollzeile:
   - V01: Indexmiete oder laufende Staffel;
   - V02: offener Vorgang;
   - V03: Miete weniger als 15 Monate unverändert – Erhöhungen wegen Modernisierung oder Betriebskosten zählen nicht;
   - V04: letztes Verlangen weniger als 1 Jahr her;
   - V05: nicht genau eine Wohnung;
   - V06: Vertragsende vor der Wirksamkeit.

   Je Kondition entsteht automatisch eine Vereinbarung „Vergleichsmiete“, und je Wohnung wird die Mietspiegel-Berechnung angelegt bzw. neu berechnet.
3. Über die Schaltfläche **Berechnungen** die Berechnungen prüfen und freigeben.
4. **Berechnen:**
   - neue Miete = der niedrigere Wert aus Vergleichsmiete und Kappung;
   - Kappung = Miete vor 3 Jahren × (1 + 20 % bzw. 15 %) + nicht anzurechnende Erhöhungen;
   - Wirksamkeit = Beginn des 3. Monats nach Zugang.

   Prüfungen V07–V12, z. B. „Kappung greift“ (Hinweis), „keine Erhöhung / unter Mindesterhöhung“ (Fehler), „über die Wirksamkeit hinaus gebucht“ (Warnung). Ein manueller Betrag mit Begründung ist möglich.

   ![Anpassungslauf Vergleichsmiete, Reiter Anpassungsvorgänge](lauf-vorgaenge.png)

   Der Reiter **Anpassungsvorgänge** zeigt je Vertrag Status, Prüfung, Vertrag, **Kondition alt**, die Mietspiegel-Berechnung, die **Vergleichsmiete**, den **Kappungsbetrag** (höchste Miete nach der Kappungsgrenze) und **Begrenzt durch** – Vergleichsmiete oder Kappungsgrenze. Im Bild begrenzt bei allen Vorgängen die Kappungsgrenze die Erhöhung.

   ![Anpassungsvorgang, Reiter Vergleichsmiete](vorgang-vergleichsmiete.png)

   Ein Doppelklick öffnet den Vorgang mit **Kondition alt** und **Kondition neu** und den Reitern **Vergleichsmiete**, **Betrag**, **Erklärung** und **Prüfung**. Der Reiter **Vergleichsmiete** zeigt die Mietspiegel-Berechnung und die Wohnung, Vergleichsmiete, **Miete vor 3 Jahren**, **Nicht anzurechnende Erhöhungen**, **Kappungsgrenze (%)**, **Kappungsbetrag** und **Begrenzt durch**. Der **Rechenweg** macht die Berechnung nachvollziehbar, im Bild:

   - Vergleichsmiete 10,95 €/m² × 57,00 m² = 624,15 €;
   - Kappung 352,83 € × (1 + 20 %) + 0,00 € = 423,40 €;
   - neue Miete = der niedrigere Wert, also 423,40 €.

   Darunter steht die **Begründung der Freigabe**.
5. **Freigeben**, optional **Probeschreiben**, dann **Ankündigen**. Das Mieterhöhungsverlangen (Bericht `comparative_rent_letter_de.odt`) enthält:
   - jetzige und neue Nettomiete und den Wirksamkeitstermin;
   - die Begründung: Mietspiegel, Version, Tabellenfeld mit Spanne, Einordnung laut Protokoll, ggf. die abweichend angenommene Miete samt Begründung;
   - Kappung, Überlegungsfrist und ein Zustimmungsformular.

   Das Original wird archiviert.

   ![Mieterhöhungsverlangen, Seite 1](mieterhoehungsverlangen.png)

   Das Beispiel zeigt die erste Seite: bisherige und neue Nettokaltmiete, Erhöhungsbetrag und „Wirksam ab“ (Beginn des dritten Kalendermonats nach Zugang; das voraussichtliche Datum wird aus dem geplanten Erklärungsdatum berechnet). Die **Begründung** nennt Mietspiegel und Version, das Tabellenfeld mit Unter-, Mittel- und Oberwert und die Einordnung in die Spanne: Wohnlage und Bezugsfertigkeit, je Merkmalgruppe die Merkmale mit + / − und dem Ergebnis der Gruppe (+1, 0, −1), den Saldo (im Beispiel +3; ausgehend vom Mittelwert 8,49 €/m² je Gruppe 20 % der Spanne bis zum Oberwert, also 8,49 + 3 × 20 % × 4,10 = 10,95 €/m²) und daraus die ortsübliche Vergleichsmiete 10,95 €/m² × 57,00 m² = 624,15 €. Die neue Miete ist durch die Kappungsgrenze auf 423,40 € begrenzt.
6. Im Reiter **Zugang / Zustimmung:**
   - **Zugangsdatum:** Daraus wird die Zustimmungsfrist berechnet (Ende des 2. Monats nach Zugang), und es entsteht eine Aufgabe.
   - **Zustimmung:** zugestimmt / teilweise (mit Betrag) / abgelehnt, mit Datum. Die Klagefrist endet 3 Monate nach der Zustimmungsfrist.

   Sind alle Entscheidungen erfasst, steht der Lauf auf **Bereit zur Ausführung**.

   ![Anpassungslauf Vergleichsmiete, Reiter Zugang / Zustimmung](lauf-zugang.png)

   Die Liste **Zugang** zeigt je Vertrag Kondition alt, **Neuer Betrag**, **Erklärungsdatum**, **Versandart**, **Zugangsdatum**, das daraus berechnete **Wirksam ab** und **Zustimmung bis** sowie **Zustimmung** und deren Datum. Im Bild: Zugang im Oktober, also Zustimmung bis 31.12. und wirksam ab 01.01. des Folgejahres.
7. **Ausführen:**
   - **Zustimmung:** Die Kondition wird zum Wirksamkeitsdatum mit dem verlangten bzw. zugestimmten Betrag geteilt.
   - **Ablehnung:** Der Vorgang erhält den Status **Abgelehnt**, und es entsteht die Aufgabe **Zustimmungsklage prüfen**. Die Klage selbst ist nicht Teil des Moduls.

![Anpassungslauf Vergleichsmiete, Reiter Protokoll](lauf-protokoll.png)

Der Reiter **Protokoll** zeigt oben die Zusammenfassung der Selektion (im Bild 15 Kandidaten, 14 neue Anpassungen, 1 ausgeschlossen) und darunter das fortlaufende Protokoll:

- je Kandidat das Ergebnis mit den Befunden, z. B. V01 (ausgeschlossen wegen aktiver Indexmiete) oder V07 (Mietspiegel-Berechnung noch nicht freigegeben – freigeben über **Berechnungen** und erneut berechnen);
- je Schritt eine Zeile mit Zeitstempel: Berechnet, Freigegeben, Angekündigt, Bereit, Ausgeführt, Abgeschlossen.

![Vertrag, Reiter Mietanpassungen mit Vergleichsmiete](vertrag-anpassung.png)

Am Vertrag steht die Vergleichsmiete im Reiter **Mietanpassungen** als eigene Mietanpassung mit Verfahren **Vergleichsmiete**, Kondition, Konditionsart und Bezugsposition (Wohnung). Sie wird bei der Selektion automatisch angelegt. Der Unterreiter **Anpassungen** zeigt die **Aktuelle Kondition** und alle Anpassungsvorgänge mit Status, Prüfung, Betrag alt, neuem Betrag und **Wirksam ab** – im Bild die ausgeführte Erhöhung von 352,83 € auf 423,40 € ab 01.01.2027.

## Rechtlicher Rahmen

| Regel | Umsetzung im Modul |
|-------|--------------------|
| Miete seit 15 Monaten unverändert, Verlangen frühestens 1 Jahr nach der letzten Erhöhung (§ 558 Abs. 1) | V03 / V04; Modernisierung und Betriebskosten ausgenommen. |
| Höchstens bis zur ortsüblichen Vergleichsmiete | Angenommene Mietspiegel-Berechnung (V07). |
| Kappungsgrenze 20 % bzw. 15 % in 3 Jahren (§ 558 Abs. 3) | Automatisch; 15 % über das Kennzeichen an der Wirtschaftseinheit. |
| Begründung in Textform, hier Mietspiegel (§ 558a) | Schreiben mit Tabellenfeld, Spanne und Einordnung; nur dieses Begründungsmittel. |
| Zustimmung bis Ende des 2. Monats nach Zugang, Klage innerhalb von 3 weiteren Monaten (§ 558b) | Fristen berechnet, Aufgaben erzeugt. |
| Wirksam ab Beginn des 3. Monats nach Zugang (§ 558b Abs. 1) | Wirksamkeitsdatum aus dem Zugang. |
| Ausgeschlossen bei Index- oder Staffelmiete (§§ 557a, 557b) | V01, Sperren. |
| Nur Wohnraum | Über die Nutzungsart. |
| Neuvermietung: Mietpreisbremse +10 % (§ 556d), sonst +20 % als Orientierung (§ 5 WiStG) | Assistent **Prüfung Vergleichsmiete Neuvermietung** am Vertragsentwurf, inkl. Modernisierungsbetrag (§ 556e Abs. 2) und Ausnahme umfassende Modernisierung (§ 556f). |

{{% callout style="warning" title="Nicht umgesetzt bzw. in der Verantwortung des Anwenders" %}}
- Gutachten oder Vergleichswohnungen als Begründung.
- Der Drittmittelabzug (§ 558 Abs. 5) ist offen.
- Die Einstufung selbst (Wohnlage, Merkmale) liegt in der Verantwortung des Vermieters.
- Die Mietspiegeldaten sind vor produktiver Nutzung gegen das amtliche Original zu prüfen.
- Die Schreibvorlage ist noch nicht juristisch geprüft.
{{% /callout %}}
