---
title: "1.2 Was ist Tryton?"
description: "Was ist Tryton? Quelloffene ERP-Software unter GPL: Herkunft, dreistufige Architektur mit trytond und sao, Module und Versionen."
weight: 20
---

**Tryton** ist eine quelloffene betriebswirtschaftliche Software (ERP) für Unternehmen jeder Größe. Sie ist vollständig Open Source unter der Lizenz GPL-3.0 und wird von einer internationalen Gemeinschaft entwickelt.

## Herkunft

Tryton entstand 2008 als Abspaltung (Fork) von TinyERP 4.2, dem Vorgänger von OpenERP bzw. Odoo. Seit 2012 steht die **Tryton Foundation**, eine belgische Stiftung, hinter dem Projekt: Sie betreut die Infrastruktur von tryton.org, die Marke Tryton und die Gemeinschaft. Professionelle Dienstleistungen bietet ein Netzwerk von Anbietern.

## Aufbau

Tryton hat eine dreistufige Architektur:

| Ebene | Umsetzung |
|-------|-----------|
| Datenbank | PostgreSQL (für Tests auch SQLite) |
| Server | **trytond**, in Python geschrieben; enthält die gesamte Geschäftslogik |
| Clients | **sao** (Web-Client im Browser) und der Desktop-Client **tryton** |

Die Funktionen sind in **Modulen** organisiert, die je nach Bedarf aktiviert werden – Rechnungswesen, Verkauf, Einkauf, Lager, Produktion, Analytik, Projekte, CRM, Zahlungsverkehr und viele mehr. Alle Module arbeiten auf denselben Daten, z. B. denselben Parteien und Konten. Das Modul **jankstar_real_estate** ergänzt Tryton um die Immobilienverwaltung und nutzt dafür die Standardmodule, vor allem das Rechnungswesen.

## Versionen

Alle sechs Monate erscheint eine neue Version (Serie), die ein Jahr gepflegt wird; jede fünfte Serie ist eine Version mit Langzeitunterstützung (LTS) für fünf Jahre – zuletzt **Tryton 8.0** (April 2026). Dieses Handbuch beschreibt **Tryton in der Version 8**.

## Weitere Informationen

- [tryton.org](https://www.tryton.org) – Projektseite mit Demo und Download
- [docs.tryton.org](https://docs.tryton.org) – Dokumentation der Standardmodule
- [discuss.tryton.org](https://discuss.tryton.org) – Forum der Gemeinschaft
