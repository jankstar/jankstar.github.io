---
title: "6.7.2 Index Rent"
description: "Index rent under § 557b BGB in Tryton: consumer price index (VPI) from Destatis, agreement, adjustment run, declaration and legal framework."
weight: 20
---

Unlike the graduated rent, an index rent is not generated in advance. Only the agreement is stored in the contract; every later increase is processed as an **adjustment run** with a letter to the tenant, receipt and execution.

## Prerequisites (once)

- **Allow the procedure:** Index rent must be allowed on the contract type, on the term type of the rent (e.g. 1000) and for the type of use of the rental object. The default data allow it for apartment, office, shop etc.
- **Index series:** menu **Immobilien › Einstellungen › Preisindizes** (Real Estate › Configuration › Price Indices), series **VPI-DE** (consumer price index Germany, Destatis 61111-0002, base 2020). For residential space only this series is allowed. For commercial space there is also **HVPI-DE**; it is maintained manually.
- **Maintain index values**, in one of three ways:
  - automatically via GENESIS-Online: the buttons **Werte abrufen** (Fetch Values) or **Ab Basisjahr abrufen** (Fetch from Base Year) on the series, or as the job `price_index_import`, e.g. on the 20th of every month because Destatis publishes in the middle of the following month (see [2.6 Jobs and Periodic Tasks]({{< relref "/tryton-real-estate/system/jobs" >}})). For this the administrator must enter the access key `genesis_token` in `trytond.conf`;
  - by CSV file with **Immobilien › Einstellungen › Indexwerte importieren** (Import Index Values; also a download from GENESIS-Online);
  - manually in the value list of the series.

  Only final values of the current base year count. Values already used by an announced or executed adjustment can no longer be changed.

## Create Contract and Agreement

![Rent adjustment Indexmiete, tab Vereinbarung](index-vereinbarung.png)

The index rent has the tabs **Vereinbarung** (Agreement), **Index**, **Anpassungen** (Adjustments: the adjustments from the adjustment runs) and **Aufgaben und Prozesse** (Tasks and Processes). In the figure the agreement is active; **Entwurf** (Draft) resets it (no longer possible once an adjustment is approved, announced or executed), **Indexmiete beenden** (Close Index Rent) closes it.

1. Create the contract with the rent term as an absolute amount as usual. Optionally, for residential space, check the initial rent with **Prüfung Vergleichsmiete Neuvermietung** (rent cap).
2. In the tab **Mietanpassungen** (Rent Adjustments) click **New**, choose the procedure **Indexmiete**, then the rent term.
3. In the tab **Vereinbarung** (Agreement): enter **Vereinbarung vom** (agreement date) and **Schriftform** (written form); both are required for residential space.
4. In the tab **Index**:
   - **Preisindex** (price index): VPI-DE.
   - **Ausgangsmonat** (base month): the index month stated in the contract. **Ausgangswert (Vertrag)** and **Basisjahr (Vertrag)** (value and base year stated in the contract) are for documentation only; the decisive **Ausgangswert** (base value) is read from the current series.
   - **Schwelle** (threshold): none, percent or index points, e.g. “only from 5 %”.
   - **Wirksamkeitsregel** (effective rule): statutory for residential space (required); for commercial space also contract month with an offset in months.
   - **Kappung anwenden** (apply cap): automatic or never.
5. Click **Aktivieren** (Activate). The system checks:
   - written form and agreement date (I01);
   - VPI for residential space (I02);
   - a value for the base month (I03);
   - no comparative rent on the term; for a modernisation only a warning appears (I10);
   - for commercial space a note on the Preisklauselgesetz (I12).

![Rent adjustment Indexmiete, tab Index](index-index.png)

Afterwards the tab shows in the area **Aktueller Stand** (current status) the **current index month** and **value**, the **last change**, the **next possible adjustment** (+12 months) and **Erklärung bis** (declaration until) – the latest dispatch date for the increase to take effect on the next possible date.

**Indexmiete beenden** (Close Index Rent) closes the agreement, e.g. for a supplement. Afterwards another procedure is possible for the term again.

## Ongoing: Increase by Adjustment Run

**Overview:** menu **Verträge › Mietanpassungen › Indexmieten - Wiedervorlage** (Contracts › Rent Adjustments › Index Rents – Follow-up) with the tabs **Fällig** (due), **Nächste 3 Monate** (next 3 months) and **Alle** (all). The task rule **Indexanpassung vorbereiten** (Prepare index rent adjustment) creates a task 14 days before **Erklärung bis**; the rule **Indexwerte veraltet** (Index values outdated) reports when a series has no current values. Both are activated in [2.5.7 Tasks and Processes]({{< relref "/tryton-real-estate/system/einstellungen/aufgaben-prozesse" >}}).

**Procedure:** menu **Verträge › Mietanpassungen › Anpassungsläufe** (Adjustment Runs), **New**, procedure **Indexmiete**.

1. **Parameters:**
   - company, optionally properties or contracts;
   - key date;
   - series;
   - index month: the last final value is proposed;
   - planned declaration date;
   - **Senkungen einbeziehen** (include decreases): off by default; decreases then only appear in the protocol.
2. **Selektieren** (Select):
   - One adjustment is created per active agreement. An agreement is skipped if an adjustment is already open (I07), the lock period has not been reached (I04) or the threshold is missed.
   - The adjustment contains reference value → new value, change in % and points, the new amount (old amount × index ratio, rounded to the cent) and the planned effective date.
   - Every check is recorded in the check protocol; the protocol of the run states the result or the reason for skipping for every agreement.
3. **Berechnen** (Calculate): optional, after corrections. If necessary, enter a manual amount with a reason.
4. **Freigeben** (Approve): adjustments with errors are cancelled. Warnings require an approval reason.
5. Optionally **Probeschreiben** (Sample Letter), then **Ankündigen** (Announce): one declaration is created per adjustment (report `index_adjustment_letter_de.odt`). It is addressed to all main tenants and contains index series and base year, old and new index month and value, the change in % and points, old and new net rent, the difference and the expected effective date. The original is archived on the adjustment and on the contract; the values are frozen from then on.
6. **Dispatch and receipt:** in the tab **Zugang / Zustimmung** (Receipts / Consents) enter the receipt date and dispatch method per adjustment, or all at once with **Zugang erfassen** (Capture Receipts). The effective date is calculated from it: the 1st of the month after next following receipt. When all receipts are entered, the run is **Bereit zur Ausführung** (ready).
7. **Ausführen** (Execute): the system checks again
   - lock period (I04), running contract and termination (I06), no posting beyond the effective date (I09), no graduated rent lock (I14);
   - the cap at the actual receipt (I11).

   It then splits the term at the effective date with the new price, recalculates the cash flows and writes the contract log. From then on the periodic posting invoices the new amount. The next adjustment refers to this index value.

The structure of adjustment run and adjustment is described in [6.7 Rent Adjustments]({{< relref "/tryton-real-estate/vertraege/mietanpassungen#adjustment-run" >}}).

![Declaration of the index adjustment (rent increase letter)](index-schreiben.png)

The letter created by **Ankündigen** (Announce) states rental object, contract number and date of the index agreement and shows in a table the index series with base year, previous and new index level, the change in % and points, previous and new net rent and the increase. It points out that the new rent applies from the start of the month after next following receipt (§ 557b para. 3 sentence 3 BGB) and that the prepayments remain unchanged.

In the example of the [adjustment]({{< relref "/tryton-real-estate/vertraege/mietanpassungen#adjustment" >}}) (state **Erledigt**, done) the index rose from 120.3 (01/2025) to 122.8 (01/2026), i.e. by 2.08 %; the rent rose from 01.01.2026 from € 600.00 to € 612.47 (600.00 × 122.8 / 120.3). With a cap rule the **counted change** (Angerechnete Veränderung) would be lower than the index change.

![Contract, tab Konditionen after the index adjustment](index-konditionen.png)

After execution the rent term is split: the previous term (sequence 10, € 600.00) ends on 31.12.2025, the new one (sequence 20, € 612.47) is valid from 01.01.2026 and stays open. The other terms, e.g. operating cost prepayments and deposit, are not affected.

## Legal Framework

**Residential space – § 557b BGB**

| Rule | Implementation in the module |
|------|------------------------------|
| Written agreement, tied to the Destatis VPI (para. 1) | Written form required (I01), only series marked “allowed for residential space” (I02). |
| Rent unchanged for at least 1 year each (para. 2 sentence 1) | I04 at selection and execution; next possible date. |
| No increase under § 558 (comparative rent); § 559 only for measures the landlord is not responsible for (para. 2) | Comparative rent locked (I10, error); modernisation only with a warning – whether the measure is permitted must be checked case by case. Graduated and index rent on the same term exclude each other. |
| Declaration in text form with index change and rent or increase as an amount of money (para. 3) | Declaration letter with all details, archived. |
| Effective from the start of the month after next following receipt (para. 3) | Effective date from the receipt date; no execution without receipt. |
| Rent cap only for the initial rent (para. 4) | Wizard **Prüfung Vergleichsmiete Neuvermietung** on the draft contract; no check for the index adjustments. |
| Deviations to the tenant's disadvantage are invalid (para. 5) | For residential space the statutory effective rule is required; deviating settings are rejected as errors. |
| Decrease when the index falls | Detected (I13); an adjustment is only created with **Senkungen einbeziehen**. |

**Commercial space – Preisklauselgesetz:** § 557b does not apply here. VPI or HVPI clauses are allowed, among others, for long-term contracts (at least 10 years or a corresponding waiver of termination). The module only gives a note (I12); the clause must be checked legally. Written form is only a warning for commercial space, and the effective date can follow the contract month.

{{% callout style="note" title="Planned, not yet in force – “Mietrecht II”" %}}
In a tight housing market, the annual index increase above 3 % is to count only by half. This is prepared as a cap rule that can be switched off (**Immobilien › Einstellungen › Kappungsregeln Indexmiete**, Index Rent Cap Rules; new rules are inactive). Decisive is the flag **Angespannter Wohnungsmarkt** (tight housing market) with validity on the property, see [5.1 Property]({{< relref "/tryton-real-estate/stammdaten/wirtschaftseinheit" >}}). Once the law is promulgated, the rule must be checked against the legal text.
{{% /callout %}}
