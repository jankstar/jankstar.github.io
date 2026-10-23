---
title: "2.4 Sequences"
description: "Sequences in Tryton: sequences of the real estate module (adjustment run, modernisation, inspection) and strict sequences for accounting and invoices."
weight: 35
---

## Sequences

Sequences assign the numbers of documents, transactions and master data of all modules.

![Administration, Sequences](nummernkreise.png)

Normal sequences are under **Verwaltung › Nummernkreise › Nummernkreise** (Administration › Sequences › Sequences). Besides those of the Tryton modules (party, shipments, purchase, sale, payment group etc.) the real estate module ships and sets up its own sequences:

| Sequence | Prefix | Example |
|----------|--------|---------|
| Anpassungslauf (adjustment run) | `AL-${year}-` | AL-2026-0001, see [6.7 Rent Adjustments]({{< relref "/tryton-real-estate/vertraege/mietanpassungen" >}}) |
| Modernisierung (modernisation) | `MOD-${year}-` | MOD-2026-0001, see [6.7.4 Modernisation]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/modernisierung" >}}) |
| Prüfvorgang (inspection) | `PR-${year}-` | PR-2026-0001, see [8.2 Performing Inspections]({{< relref "/tryton-real-estate/instandhaltung/pruefungen" >}}) |

`${year}` is replaced by the current year, the running number has four digits with leading zeros. Prefix and padding can be adapted if required.

## Strict Sequences

![Administration, Strict Sequences](strikte-nummernkreise.png)

Accounting needs **strict sequences** (Strikte Nummernkreise): they assign consecutive numbers without gaps, as required for account moves and invoices. They are under **Verwaltung › Nummernkreise › Strikte Nummernkreise** (Administration › Sequences › Strict Sequences); normal sequences under **Nummernkreise**, the kinds under **Typen** (types).

Typically there is one strict sequence per fiscal year for:

| Sequence | Sequence type |
|----------|---------------|
| Buchungssatz (account move) | Buchungssatz |
| Lieferantenrechnung (supplier invoice) | Rechnung (invoice) |
| Kundenrechnung (customer invoice) | Rechnung |
| Lieferantenrechnung Storno (supplier credit note) | Rechnung |
| Kundenrechnung Storno (customer credit note) | Rechnung |

In the figure the names carry the year and a running number (25/1 … 25/5), all of type **Inkrementell** (incremental).

![Strict sequence Buchungssatz](strikter-nummernkreis.png)

A strict sequence has:

- **Name Nummernkreis** (name) and **Nummernkreistyp** (sequence type, e.g. Buchungssatz or Rechnung), **Aktiv**;
- **Unternehmen** (company): every sequence is assigned to a company; with several companies each has its own sequences;
- **Präfix** and **Suffix** (prefix, suffix), e.g. prefix 251 for year 25, sequence 1;
- **Typ** (incremental), **Feste Länge (Vornullen)** (padding), **Schrittweite** (increment) and **Nächste Nummer** (next number);
- **Vorschau** (preview) of the next number, in the figure 25100001.

The strict sequences for accounting are assigned in the fiscal year, see [2.5.1 Accounting]({{< relref "/tryton-real-estate/system/einstellungen/rechnungswesen" >}}).
