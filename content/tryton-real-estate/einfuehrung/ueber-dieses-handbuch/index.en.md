---
title: "1.1 About this Manual"
description: "Purpose, audience, structure and conventions of the manual for real estate management with Tryton; note on its creation with AI support."
weight: 10
---

This manual describes working with the Tryton module **jankstar_real_estate** for real estate management – from master data via contracts, rent adjustments and operating costs to maintenance, tasks and correspondence.

## Audience

- **Clerks:** the main audience. The manual guides through the processes of real estate management from A to Z.
- **Administration:** installation, modules, users, sequences and settings in chapter 2.
- **Accounting:** basics in chapter 4 for everyone who also handles the accounting of the properties; the automatic postings of the module are explained with each process.

## Structure

| Chapter | Content |
|---------|---------|
| 1 Introduction | this manual, Tryton, using Tryton, the module at a glance |
| 2 System and Requirements | installation, users, sequences, settings, jobs |
| 3 Business Partners and Companies | parties, companies, party roles |
| 4 Accounting | invoices, payments, postings, closing |
| 5 Real Estate Master Data | property, buildings, rental objects, equipment, meters, option rates |
| 6 Contracts | contract, terms, periodic posting, rent adjustments |
| 7 Operating Costs | billing units to the operating cost settlement |
| 8 Maintenance | inspection plans, inspections, defects |
| 9 Construction and Project System | projects |
| 10 Tasks and Processes | working with tasks and processes |
| 11 Information System and Correspondence | reports and templates |
| Glossary | technical terms German/English |

The manual is extended continuously; chapters and pages without content are still hidden.

## Conventions

- Every page starts with the entry (menu, list) and then describes form, tabs and actions; the figures are numbered per page.
- **Menu paths** are bold with “›”, e.g. **Immobilien › Stammdaten › Wirtschaftseinheit**.
- **Field and button names** are bold as they appear in the (German) user interface, followed by the English translation in brackets.
- Topics of standard Tryton (parties, companies, users, accounting) are only described in overview as far as they matter for real estate management; details are in the [Tryton documentation](https://docs.tryton.org).
- The examples come from a demo database; names and numbers are fictitious.

## Notes on Creation and Disclaimer

The texts and image editing of this manual were created with the support of AI (Claude, Anthropic). Content and details were reviewed by the author to the best of his knowledge.

Despite the care taken in creating text, figures and program code, no legal responsibility or liability of any kind is accepted for possible errors and their consequences.

TRYTON® is a registered trademark of the Tryton Foundation.

{{% callout style="warning" %}}
Installation and configuration in chapter 2 describe a development or test system. Every operator is responsible for the security of their own systems.
{{% /callout %}}
