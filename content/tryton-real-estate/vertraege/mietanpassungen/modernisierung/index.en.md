---
title: "6.7.4 Modernisation"
description: "Rent increase after modernisation (§§ 555b–559e BGB) in Tryton: measure, costs, allocation, announcement, hardship objection and adjustment run."
weight: 40
---

After a modernisation the landlord may increase the annual rent by a share of the costs (§§ 555b–555f, 559–559e BGB). Unlike index and comparative rent, the starting point is not a single term but a **measure** on buildings of a property whose costs are allocated to several rental units. The modernisation therefore has two phases:

1. the **measure** – planning, announcement, work, costs;
2. the **rent increase** by [adjustment run]({{< relref "/tryton-real-estate/vertraege/mietanpassungen#adjustment-run" >}}) – increase declaration and split of the rent term.

A process with tasks and deadlines accompanies both phases.

## Prerequisites (once)

- **Modernisation rule** under **Immobilien › Einstellungen › Modernisierungsregeln** (Real Estate › Configuration › Modernisation Rules; group real estate administration only). The statutory values as of 01.01.2024 are shipped:
  - rate 8 %, heating 10 %;
  - simplified procedure up to 10,000 € per apartment, with a maintenance flat rate of 30 % (heating 15 %) and a lock of 5 years;
  - cap 3.00 €/m², or 2.00 €/m² for a rent below 7.00 €/m², within 6 years; heating additionally 0.50 €/m²;
  - announcement period 3 months, extension 6 months, tolerance 10 %.

  The rule valid on the announcement date applies (otherwise on the planned start).

  {{% callout style="warning" title="Set the cap measurement" %}}
  Set the **Kappungs-Bemessung** (cap measurement) once in the rule, e.g. living area. It provides the area for the cap and the increase per m² in the letter. Without it **Ankündigen** (Announce) is not possible (M02).
  {{% /callout %}}
- **Allow the procedure:** modernisation on the contract type and on the term type of the rent, see [2.5.4 Contracts]({{< relref "/tryton-real-estate/system/einstellungen/vertraege" >}}). Only such a term becomes the rent term of an apartment.
- **Maintain measurements:** the measurement for the allocation key on the apartments, usually living area.
- For the planning calculation (optional): rent survey on the property and classification of the objects, see [6.7.3 Comparative Rent]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/vergleichsmiete" >}}).

## Create and Plan the Measure

Menu **Verträge › Mietanpassungen › Modernisierungen** (Modernisations) with the tabs Planning, In Progress, Completed, Settled and All; **New** creates a measure (number MOD-year-no.). The process **Modernisierung** starts automatically and is shown in the tab **Ablauf** (workflow).

1. Tab **Maßnahme** (measure):
   - name, company, property, buildings (empty = all buildings of the property);
   - **measure types** according to § 555b BGB: no. 1 energetic, 1a heating system, 2 primary energy/climate protection, 3 water, 4 utility value, 4a fibre optics, 5 living conditions, 6 not attributable to the landlord, 7 new living space – nos. 2 and 7 alone do not allow an increase;
   - **variant:** Standard (§ 559, 8 %), Simplified (§ 559c) or Heating system (§ 559e, 10 %);
   - description and energy saving (flat values allowed);
   - flags **Bagatelle** (trivial measure, § 555c para. 4, with reason), **Leerstand** (vacancy), **Umfassende Modernisierung** (comprehensive modernisation, § 556f, with reason), **Nicht vom Vermieter zu vertreten** (not attributable to the landlord) and **JAZ Wärmepumpe nachgewiesen** (heat pump SPF proven).
2. Tab **Termine** (schedule): planned start and duration, later actual start and end.
3. Tab **Kosten** (costs):
   - cost lines **per trade**, not per invoice, e.g. facade, windows, heating: kind planned, later actual; net, tax, gross; measure type; **maintenance share** (none, percent or net amount – only for the standard variant, otherwise the flat rate applies) and optionally a single rental unit, e.g. for a balcony.
   - **Expected costs** and **expected operating costs**; both belong in the announcement.
4. Tab **Drittmittel** (third-party funds, § 559a): a grant or third-party payment reduces the costs, the interest advantage of a loan reduces the annual amount; optionally for a single rental unit.
5. **Allocation key:** equal shares, measurement (default, with a selectable measurement type, e.g. living area or number of windows) or manual (quantity per rental unit, reason required). The share is quantity / total of the participating units.
6. **Mietobjekte ermitteln** (Determine Rental Units): the module takes over the apartments of the buildings with contract, rent term and quantity; running it again updates them and keeps manual settings. In the tab **Mietobjekte** you can correct per rental unit:
   - **In Verteilung** (in allocation): deselect only with a reason – the share of the others increases; a vacant unit always takes part, its share is borne by the owner (M09);
   - **Vorsteuerabzug** (input tax deduction): costs net instead of gross; proposed if a term of the contract carries VAT;
   - **Keine Erhöhung** (no increase) with a reason (vacancy, commercial, graduated/index rent, hardship accepted, termination, waiver …): the unit stays in the allocation but gets no increase and no letter. Vacancy and commercial are set automatically.

   Per rental unit you see share, allocable costs and the **expected increase** per month after the cap.
7. **Planning calculation** (optional, tab **Kalkulation**, in planning and draft): enter **planned features** per building and apartment – rent survey classes or features (add/remove) and measurements after the modernisation, e.g. the new energy consumption value. **Kalkulieren** (Calculate) shows per apartment:
   - increase according to § 559 and the rent afterwards;
   - comparative rent before and after, the rent according to § 558 and from when it can be requested;
   - limit for a new letting;
   - additional rent p. a. and yield.

   This lets you decide whether the cost allocation according to § 559 or a later comparative rent in the modernised state (§ 558) yields more.
8. **Planung übernehmen** (Take Over Planning): state planning → draft.

### Calculation of the Allocation

1. Share per participating rental unit by the key; costs and funds of a single unit go to that unit only.
2. Cost basis net or gross (input tax deduction).
3. Allocable costs = costs − maintenance share (percent, amount or flat rate 30 % / 15 %) − grants and third-party payments.
4. Annual amount = allocable costs × 8 % or 10 % − interest advantage; monthly amount = annual amount / 12.
5. Cap: area × 3.00 €/m² (2.00 €/m² for a rent below 7.00 €/m²), heating additionally area × 0.50 €/m²; previous increases within the last 6 years are taken into account by the adjustment run.

Example: building with 420 m², costs 180,000 €, maintenance share 25 %, grant 15,000 € → allocable 120,000 €; an apartment with 64.20 m² bears 18,342.86 € × 8 % = 1,467.43 € per year, i.e. **122.29 € per month**.

## Announcement (§ 555c BGB)

1. Optionally **Probeschreiben** (Sample Letter): the announcement of the first rental unit marked ENTWURF, not archived.
2. **Ankündigen** (Announce). The module checks:
   - M01: at least one measure type allowing an increase;
   - M02: required details – description, planned start and duration, expected costs and operating costs, cap measurement;
   - M03: start at the earliest 3 months after the announcement;
   - M04: heating only with no. 1a;
   - M05: limit of the simplified procedure (warning);
   - M10: quantities for the key measurement or manual.

   Then a letter to all main tenants is created per rental unit with an increase (report **Modernisierungsankündigung**) with kind and scope, energy saving, start and duration, the expected increase per month and per m², the future operating costs, the note on the hardship objection with form and deadline (§ 555d) and the special termination right (§ 555e). The letters are archived on the rental unit and on the contract; the announced increase is frozen.
3. **Zugang erfassen** (Capture Receipts), for all units at once or per unit. From this the module calculates the deadlines **Härteeinwand bis** (hardship objection until) and **Sonderkündigung bis** (special termination until): end of the month after receipt. On the rental unit you also enter **Ankündigung mangelhaft** (announcement defective), **Härtehinweis fehlt** (hardship notice missing; then no hardship deadline), the hardship objection with date, note and decision, and **Gekündigt** (terminated).

Exceptions: trivial measures (§ 555c para. 4) and vacancy are not announced – the work starts directly.

## Execution of the Work

1. **Arbeiten beginnen** (Start Work). M06 warns if the start is less than 3 months after the receipt of the announcement.
2. During the work enter the **actual costs**, preferably with reference to the supplier invoice line; net amount and tax are taken over. The measure is then visible and searchable on the invoice line.
3. **Fertigstellen** (Complete). M07 checks that costs exist and, for the standard variant, that every line has a maintenance share. From then on the tab **Prüfmeldungen** (check messages) shows M08: the input tax of the rental units compared with the option rate of the property (§ 15a UStG, consult a tax advisor); without a deviation it says “Keine Meldungen.”
4. When using the rent survey, **Geplante Merkmale übernehmen** (Apply Planned Features): the planned classes, features and measurements apply to the objects from the completion (process step 90).

A vacancy measure is settled immediately on completion.

## Rent Increase (§§ 559, 559b BGB)

1. In the completed measure click **Anpassungslauf anlegen** (Create Adjustment Run). The run opens with the measure as parameter; key date and declaration date are today, at the earliest the completion.
2. **Selektieren** (Select): one adjustment per rental unit. Skipped with a reason in the protocol: no increase, no contract or rent term, already an adjustment for the unit, procedure not allowed.
3. **Berechnen** (Calculate):
   - increase = allocable costs × 8 % / 10 %, minus interest advantage; the actual costs are used, as long as there are none the planned costs;
   - cap with the previous increases of the apartment within the last 6 years (heating separately);
   - effective from the start of the 3rd month after receipt, **+ 6 months** without announcement or its receipt, with a defective announcement or more than 10 % above the announced increase.

   The values are shown on the adjustment in the tab **Modernisierung**. Checks E01–E14, e.g. graduated rent (E02), index rent only for circumstances not attributable to the landlord (E03), hardship objection open (E04), cap applies (E05), postponed by 6 months (E07), no cap area (E10), termination (E11), declaration before completion (E12).
4. **Freigeben** (Approve), optionally **Probeschreiben**, then **Ankündigen**. The increase declaration (report **Modernisierungs-Mieterhöhung**) contains the reference to the announcement, costs per trade, maintenance share, third-party funds, key and share, allocable costs, annual and monthly amount, old and new rent, effective date, cap or extension and the special termination right according to § 561. **Schreiben drucken** (Print Letters) prints all letters still to be sent.
5. In the tab **Zugang / Zustimmung** capture the receipt; the module calculates **Sonderkündigung bis** (end of the second month after receipt, § 561). The tenant's consent is not required; the increase is a unilateral declaration. If the tenant has terminated according to § 561, set **Gekündigt** and cancel the adjustment.
6. **Ausführen** (Execute, in the background): the term is split at the effective date with the new rent; errors are written to the protocol. When the run is closed, the measure changes to **Abgerechnet** (settled).

The rental unit of the measure then shows the adjustment and the state of the increase.

## Process and Deadlines

| No. | Process step | Completed by |
|-----|--------------|--------------|
| 10 | Plan and calculate (optional) | state draft |
| 20 | Determine rental units | rental units exist |
| 30 | Announce the measure | state announced; not for vacancy and trivial measures |
| 40 | Capture receipts of the announcement | receipt at every unit with an increase |
| 50 | Handle hardship objections and special terminations | deadlines expired, no objection open |
| 60 | Start the work | state in progress |
| 70 | Complete the work, determine the costs | state completed |
| 80 | Start the adjustment run | measure settled; not for vacancy |
| 90 | Update rent survey features | manual; only with a rent survey |

Deadline tasks: hardship objection (per rental unit), start of work and increase declaration (on the measure) and special termination according to § 561 (on the adjustment), see [2.5.7 Tasks and Processes]({{< relref "/tryton-real-estate/system/einstellungen/aufgaben-prozesse" >}}). The adjustment run has its own process **Anpassungslauf Modernisierung**.

## Further Support

- **Vacancy:** in the process **Leerstand / Neuvermietung** (vacancy / new letting) of the rental unit the optional step **Modernisierung planen** creates a vacancy measure (wizard **Modernisierung planen (Leerstand)**), see [5.3 Rental Objects]({{< relref "/tryton-real-estate/stammdaten/mietobjekt" >}}). It is carried out without announcement and settled on completion. Its amount counts for the next letting.
- **New letting:** the wizard **Prüfung Vergleichsmiete Neuvermietung** raises the limit of the rent cap by the modernisation amount of the last 3 years (§ 556e para. 2). After a comprehensive modernisation there is no limit for the first letting (§ 556f).

{{% callout style="warning" title="Not covered" %}}
- Commercial leases – there only the contract applies, possibly with a free adjustment.
- The legal review of the letter templates.
- For heating systems with a heat pump the 50 % rule must be checked manually (E08).
{{% /callout %}}
