---
title: "6.7 Mietanpassungen"
weight: 70
---

Eine Mietanpassung ändert genau eine Kondition eines Vertrags nach einem bestimmten Verfahren. Hier ist beschrieben, was für alle Verfahren gilt; die einzelnen Verfahren stehen auf den Unterseiten.

## Verfahren

| Verfahren | Rechtsgrundlage | Ablauf |
|-----------|-----------------|--------|
| [6.7.1 Staffelmiete]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/staffelmiete" >}}) | § 557a BGB | Alle Staffeln werden bei Vertragsschluss als eigene Konditionen erzeugt. |
| [6.7.2 Indexmiete]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/indexmiete" >}}) | § 557b BGB | Vereinbarung am Vertrag; jede Erhöhung per Anpassungslauf mit Erklärung, Zugang und Ausführung. |
| [6.7.3 Vergleichsmiete]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/vergleichsmiete" >}}) | §§ 558–558b BGB | Anpassungslauf auf Grundlage der Mietspiegel-Berechnungen, mit Kappung und Zustimmung des Mieters. |
| [6.7.4 Modernisierung]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/modernisierung" >}}) | §§ 555b–559e BGB | Maßnahme mit Kosten und Ankündigung; Erhöhung per Anpassungslauf. |
| [6.7.5 Betriebskosten und freie Anpassung]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/betriebskosten" >}}) | § 560 BGB | Anpassung von Vorauszahlungen und Pauschalen; freie Anpassung. |

## Zulassung

Ein Verfahren ist nur möglich, wenn es an der **Vertragsart**, an der **Konditionsart** und bei der **Nutzungsart** mindestens eines Mietobjekts der Vertragsposition unter **Zulässige Anpassungsverfahren** eingetragen ist, siehe [2.4.4 Verträge]({{< relref "/tryton-real-estate/system/einstellungen/vertraege" >}}). In den Standarddaten erlaubt die Nutzungsart Wohnung alle Verfahren; Büro, Laden, Lager, Stellplatz und Garage alle außer Vergleichsmiete und Modernisierung – die Vergleichsmiete (§ 558 BGB) gibt es nur für Wohnungen. Je Kondition ist höchstens ein vereinbartes Verfahren möglich, also Staffel- **oder** Indexmiete.

## Menüs

Unter **Immobilien › Verträge › Mietanpassungen**, in der Reihenfolge der Arbeit:

| Menü | Inhalt |
|------|--------|
| Vereinbarungen | Die Mietanpassungen aller Verfahren. |
| Indexmieten - Wiedervorlage | Aktive Indexmieten mit den Reitern Fällig, Nächste 3 Monate und Alle, siehe 6.7.2. |
| Anpassungsläufe | Der zentrale Einstieg für Index-, Vergleichsmiete und Modernisierung, siehe unten. |
| Anpassungen | Alle Anpassungsvorgänge, siehe unten. |
| Mietspiegel-Berechnungen | Berechnungen der Vergleichsmiete, siehe 6.7.3. |
| Modernisierungen | Modernisierungsmaßnahmen, siehe 6.7.4. |

Alle Listen haben oben die Auswahl **Gesellschaft** (Pflicht) und **Wirtschaftseinheit** (optional). Die Einstellungen – Preisindizes, Indexwerte importieren, Kappungsregeln Indexmiete, Modernisierungsregeln – stehen unter **Immobilien › Einstellungen**.

## Reiter Mietanpassungen am Vertrag

![Vertrag, Reiter Mietanpassungen](reiter-mietanpassungen.png)

Der Reiter **Mietanpassungen** des Vertrags listet alle Mietanpassungen mit Wirtschaftseinheit, Verfahren, Kondition, **Gültig ab**, **Vereinbarung vom**, **Endbetrag** (bei der Staffelmiete der Betrag der letzten Staffel), **Erklärung bis** (bei der Indexmiete der späteste Versandtag der Erklärung), **Status** und **Gebucht** (mindestens eine Stufe ist bereits gebucht). Mit **+** wird eine neue Mietanpassung angelegt, ein Doppelklick öffnet sie. Kopf und Reiter der Mietanpassung hängen vom Verfahren ab.

## Anpassungslauf

Index-, Vergleichsmiete und Modernisierung werden über einen **Anpassungslauf** umgesetzt: Menü **Verträge › Mietanpassungen › Anpassungsläufe**, **Neu**, Verfahren wählen. Ein Lauf (Nummer AL-Jahr-Nr.) hat Verfahren, Gesellschaft, optional Wirtschaftseinheiten und Verträge als Filter, Stichtag, geplantes Erklärungsdatum und die Parameter des Verfahrens.

![Anpassungslauf, Reiter Ablauf](anpassungslauf.png)

Der Lauf hat die Reiter **Ablauf**, **Parameter**, **Anpassungsvorgänge**, **Zugang / Zustimmung** und **Protokoll**; **Fortschritt** zählt die Vorgänge je Stand. Mit dem ersten Selektieren startet der Prozess **Anpassungslauf Indexmiete** bzw. **Vergleichsmiete**; der Reiter **Ablauf** zeigt seine sechs Schritte mit Fälligkeit – Parameter prüfen und selektieren, Vorschläge prüfen, bearbeiten und berechnen, Freigeben, Ankündigen (Schreiben), Zugang erfassen, Ausführen. Die Schritte werden mit dem Status des Laufs automatisch erledigt (im Bild ist der Lauf **Selektiert**, Schritt 10 erledigt).

| Schritt | Wirkung |
|---------|---------|
| Selektieren | Je passender Vereinbarung bzw. Kondition entsteht ein Vorgang im Entwurf; jeder Kandidat bekommt eine Protokollzeile mit Ergebnis oder Grund des Auslassens. |
| Konditionen hinzufügen | Einzelne Konditionen mit den Prüfungen der Selektion nachnehmen. |
| Berechnen | Werte und Prüfungen der Vorgänge neu berechnen. Ein manueller Betrag mit Begründung ist möglich, aber nur zwischen bisherigem und berechnetem Betrag. |
| Freigeben | Vorgänge ohne Befund werden freigegeben, mit Warnung nur mit **Begründung der Freigabe**; die übrigen werden storniert. |
| Probeschreiben | Zeigt das Schreiben eines Vorgangs als ENTWURF, ohne es zu archivieren. |
| Ankündigen | Erzeugt und archiviert die Schreiben; **Schreiben drucken** gibt alle noch zu versendenden in einem Dokument aus. |
| Zugang / Zustimmung | Zugangsdatum, Versandart und – bei der Vergleichsmiete – Zustimmung je Vorgang, oder gesammelt mit **Zugang erfassen**. Sind alle erfasst, steht der Lauf auf **Bereit zur Ausführung**. |
| Ausführen | Läuft im Hintergrund: teilt die Kondition zum Wirksamkeitsdatum; Fehler werden ins Protokoll geschrieben. Den Lauf danach neu laden. |
| Auf Entwurf zurücksetzen / Stornieren | Löscht die Entwürfe bzw. storniert die offenen Vorgänge und den Prozess. |

## Anpassungsvorgang

![Anpassungsvorgang, Reiter Index](anpassungsvorgang.png)

Alle Anpassungsvorgänge listet das Menü **Verträge › Mietanpassungen › Anpassungen**; ein Vorgang ist auch vom Lauf aus oder am Vertrag (Mietanpassung, Reiter **Anpassungen**) zu öffnen. Er zeigt den Status, die Mietanpassung, den Lauf, die **Kondition alt** und die **Kondition neu** mit Gültigkeit und Betrag sowie Reiter je Verfahren – im Bild für die Indexmiete **Index**, **Betrag**, **Erklärung** und **Prüfung** (Prüfprotokoll). Das angekündigte Schreiben hängt als Anhang am Vorgang (Büroklammer in der Symbolleiste). Die Status sind Entwurf, Genehmigt, Erklärt, Erledigt, Storniert und – bei der Vergleichsmiete – Abgelehnt.
