---
title: "1.2 What is Tryton?"
description: "What is Tryton? Open source ERP software under GPL: origin, three-tier architecture with trytond and sao, modules and versions."
weight: 20
---

**Tryton** is open source business software (ERP) for companies of any size. It is completely open source under the GPL-3.0 licence and developed by an international community.

## Origin

Tryton started in 2008 as a fork of TinyERP 4.2, the predecessor of OpenERP and Odoo. Since 2012 the **Tryton Foundation**, a Belgian foundation, has backed the project: it runs the tryton.org infrastructure and manages the Tryton trademark and the community. Professional services are offered by a network of providers.

## Architecture

Tryton has a three-tier architecture:

| Tier | Implementation |
|------|----------------|
| Database | PostgreSQL (SQLite for testing) |
| Server | **trytond**, written in Python; contains all business logic |
| Clients | **sao** (web client in the browser) and the desktop client **tryton** |

The functions are organised in **modules** that are activated as required – accounting, sales, purchasing, stock, production, analytics, projects, CRM, payments and many more. All modules work on the same data, e.g. the same parties and accounts. The module **jankstar_real_estate** adds real estate management to Tryton and uses the standard modules for it, above all accounting.

## Versions

A new version (series) is released every six months and maintained for one year; every fifth series is a long-term support (LTS) release supported for five years – most recently **Tryton 8.0** (April 2026). This manual describes **Tryton version 8**.

## Further Information

- [tryton.org](https://www.tryton.org) – project site with demo and download
- [docs.tryton.org](https://docs.tryton.org) – documentation of the standard modules
- [discuss.tryton.org](https://discuss.tryton.org) – community forum
