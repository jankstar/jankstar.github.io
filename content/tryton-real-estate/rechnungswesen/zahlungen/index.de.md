---
title: "4.2 Zahlungen"
description: "Zahlungen in Tryton: Hausbank, SEPA-Überweisung und -Lastschrift mit Zwischenkonto Bankbewegung, Kontoauszug und Rücklastschrift."
weight: 20
---

{{% callout style="info" %}}
Diese Seite ist noch nicht vollständig und wird ergänzt.
{{% /callout %}}

## Hausbank und Überweisung/Lastschrift

Überweisungen (Zahlungsausgang) und Lastschriften (Zahlungseingang) laufen über den SEPA-Zahlungsverkehr. Gebucht wird in zwei Schritten: Zuerst bucht die Zahlung gegen das Zwischenkonto **Bankbewegung (Geldtransit)**, erst der Kontoauszug der Bank bucht vom Zwischenkonto auf das Bankkonto. Was auf dem Zwischenkonto offen bleibt, hat die Bank noch nicht bestätigt.

### Einrichtung (einmalig, in dieser Reihenfolge)

1. **Sachkonten** im Kontenplan: Bankkonto (z. B. 2741) und Zwischenkonto **2749 Bankbewegung (Geldtransit)** mit **Abstimmen**, siehe [2.5.1 Rechnungswesen – Bank und Zahlungsverkehr]({{< relref "/tryton-real-estate/system/einstellungen/rechnungswesen" >}}).
2. **Bank und Bankkonto** unter **Banking**: die Bank als Partei mit BIC, das Bankkonto mit IBAN, Inhaber ist die Partei der Gesellschaft.
3. **Gläubiger-Identifikationsnummer** (SEPA) an der Partei der Gesellschaft im Reiter **Identifikatoren** – Voraussetzung für Lastschriften.
4. **Journale:** ein Journal **Bank** (Typ Kontoauszug) für den Kontoauszug und ein Journal **Zahlungsverkehr** (Typ Allgemein) für die Zwischenbuchungen.
5. **Kontoauszugsbuch** (Einstellungen › Auszüge): Journal Bank, Bankkonto (IBAN), Sachkonto Bank (2741), Prüfung über den Saldo.
6. **Zahlungsjournale** (Einstellungen › Zahlungen), je eines für **SEPA-Überweisung** (pain.001) und **SEPA-Lastschrift** (pain.008, CORE) – jeweils mit Bankkonto, **Verrechnungskonto 2749 Bankbewegung** und Verrechnungsjournal Zahlungsverkehr.
7. **Stammdaten der Mieter und Lieferanten:** IBAN an der Partei im Reiter **Banking**; für Mieter mit Lastschrift ein gültiges **SEPA-Mandat** (Rechnungswesen › Zahlungen › Mandate).

### Überweisung (Zahlungsausgang)

1. Offene Verbindlichkeiten auswählen und **Positionen bezahlen** – es entstehen Zahlungen im Entwurf.
2. Zahlungen **genehmigen** und **verarbeiten**: Es entsteht eine SEPA-Datei (pain.001) für das Online-Banking.
3. Ist die Zahlung **Erfolgreich** (manuell oder per eingelesener Bankmeldung CAMT.054), bucht Tryton im Journal Zahlungsverkehr *Verbindlichkeit an 2749 Bankbewegung*; der Posten des Lieferanten ist ausgeglichen.
4. Mit dem **Kontoauszug** (z. B. CAMT.053) wird die Auszugszeile der Zahlung bzw. dem Zahlungslauf zugeordnet: *2749 Bankbewegung an Bank* – das Zwischenkonto ist wieder ausgeglichen.

### Lastschrift (Zahlungseingang)

1. **Lastschriften erzeugen** für fällige Mietforderungen von Mietern mit gültigem Mandat.
2. Zahlungen **genehmigen** und **verarbeiten**: SEPA-Datei pain.008 für die Bank.
3. Bei **Erfolgreich** bucht Tryton *2749 Bankbewegung an Mietforderung* (z. B. 2000).
4. Der **Kontoauszug** bucht *Bank an 2749 Bankbewegung*.

Eine **Rücklastschrift** wird erfasst, indem die Zahlung auf **Fehlgeschlagen** gesetzt wird: Tryton storniert die Zwischenbuchung, die Forderung ist wieder offen.

## Manuell

## Zahllauf und Lastschrift

## Kontoauszug
