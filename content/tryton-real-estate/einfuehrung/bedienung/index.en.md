---
title: "1.3 Using Tryton"
description: "Using Tryton in the web client sao: menu, list and filter, toolbar and the menu at the title of the tab."
weight: 30
---

Operation is the same in all areas of Tryton. The figures show the web client sao; the desktop client has the same structure.

## Window Layout

![Window with menu, list and toolbar](fenster-liste.png)

- **Menu (left):** the areas, e.g. Parteien, Immobilien, Rechnungswesen, Verwaltung. **›** expands folders, a click on an entry opens it as a **tab** on the right. The **star** adds an entry to the favourites.
- **Aktion (top):** search field for menu entries and favourites – type a name and select it without clicking through the menu. **≡ Tryton** shows and hides the menu.
- **Tabs:** every opened menu entry appears as a tab at the top; **×** closes it.
- **User menu (top right):** name and company [currency], below notifications, **Einstellungen…** (preferences, e.g. language, current company), **Hilfe…** (help) and **Abmelden** (log out).

## List and Filter

Most menu entries first open a **list**. A double click on a row opens the form of the record.

![List with filter and toolbar](liste-filter.png)

- **Filter:** enter a search term or condition, e.g. a name or `Name: Immo*`; the **magnifier** searches, matching fields are suggested while typing.
- **Star:** save the current filter as a bookmark; **bookmark icon:** call saved filters.
- **Archive icon:** also show inactive (archived) records.
- **‹ ›** next to the filter: page through long lists.
- **≡** at the left of the column header: show and hide columns; a click on a column header sorts.
- **Check boxes:** select records – actions, printing etc. then apply to all selected ones.

## Toolbar

![Toolbar](symbolleiste.png)

| Button | Function |
|--------|----------|
| <img src="/images/tryton-icons/ansicht.png" alt="Switch view" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Ansicht wechseln (switch view) | switch between list and form |
| <img src="/images/tryton-icons/vorheriger.png" alt="Previous" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> <img src="/images/tryton-icons/naechster.png" alt="Next" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Vorheriger / Nächster (previous / next) | go to the previous or next record |
| <img src="/images/tryton-icons/zaehler.png" alt="Counter" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Counter | current record / number in the list |
| <img src="/images/tryton-icons/neu.png" alt="New" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Neu (new) | create a new record |
| <img src="/images/tryton-icons/speichern.png" alt="Save" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Speichern (save) | save changes |
| <img src="/images/tryton-icons/neuladen.png" alt="Reload" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Neu laden/Rückgängig (reload/undo) | reload or discard unsaved changes |
| <img src="/images/tryton-icons/anhang.png" alt="Attachment" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Anhang (attachment) | show and add attachments (files, links); the number on the icon shows existing attachments |
| <img src="/images/tryton-icons/notiz.png" alt="Note" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Notiz (note) | notes on the record |
| <img src="/images/tryton-icons/aktion.png" alt="Action" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Aktion (action) | wizards and actions of the record, e.g. calculations |
| <img src="/images/tryton-icons/verknuepfen.png" alt="Relate" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Verknüpfen (relate) | jump to related records, e.g. from the contract to invoices |
| <img src="/images/tryton-icons/drucken.png" alt="Print" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> Drucken (print) | print reports and letters |
| <img src="/images/tryton-icons/email.png" alt="E-mail" style="height:1.6em;display:inline;margin:0 .3em 0 0;vertical-align:middle;box-shadow:none"> E-Mail | create an e-mail with a report as attachment |

## Menu at the Title

![Menu at the title of the tab](menue-ueberschrift.png)

A click on the **title** of the tab (e.g. **Parteien ▾**) opens a menu with all functions of the toolbar and some more:

| Group | Functions |
|-------|-----------|
| Navigation | switch view, previous, next, search |
| Editing | new, save, reload/undo, **Duplizieren** (duplicate), **Löschen** (delete) |
| Log | **Logs anzeigen…** (who created or changed when), **Bearbeitungen anzeigen…** (revisions, if history is active for the model) |
| Extras | attachment, note, action |
| Output | relate, print, e-mail |
| Data | **Export** (export records e.g. as CSV), **Import** (import records from CSV) |
| | **Registerkarte schließen** (close tab) |

**Duplizieren**, **Löschen**, **Logs**, **Export** and **Import** are only available in this menu.
