---
title: "4.2 Payments"
description: "Payments in Tryton: house bank, SEPA credit transfer and direct debit with transit account, bank statement and returned debits."
weight: 20
---

{{% callout style="info" %}}
This page is not complete yet and will be extended.
{{% /callout %}}

## House Bank and Credit Transfer/Direct Debit

Credit transfers (outgoing) and direct debits (incoming) run via SEPA payments. Posting takes two steps: first the payment is posted against the transit account **Bankbewegung (Geldtransit)**, only the bank statement posts from the transit account to the bank account. What remains open on the transit account has not yet been confirmed by the bank.

### Setup (once, in this order)

1. **Accounts** in the chart of accounts: bank account (e.g. 2741) and transit account **2749 Bankbewegung (Geldtransit)** with **Abstimmen** (reconcile), see [2.5.1 Accounting – Bank and Payments]({{< relref "/tryton-real-estate/system/einstellungen/rechnungswesen" >}}).
2. **Bank and bank account** under **Banking**: the bank as a party with BIC, the bank account with IBAN, owner is the party of the company.
3. **SEPA creditor identifier** on the party of the company in the tab **Identifikatoren** – required for direct debits.
4. **Journals:** a journal **Bank** (type statement) for the bank statement and a journal **Zahlungsverkehr** (type general) for the transit postings.
5. **Statement journal** (Configuration › Statements): journal Bank, bank account (IBAN), bank account in the chart (2741), validation by balance.
6. **Payment journals** (Configuration › Payments), one each for **SEPA credit transfer** (pain.001) and **SEPA direct debit** (pain.008, CORE) – each with bank account, **clearing account 2749 Bankbewegung** and clearing journal Zahlungsverkehr.
7. **Master data of tenants and suppliers:** IBAN on the party in the tab **Banking**; for tenants paying by direct debit a valid **SEPA mandate** (Accounting › Payments › Mandates).

### Credit Transfer (Outgoing)

1. Select open payables and **Pay Lines** – payments are created as drafts.
2. **Approve** and **process** the payments: a SEPA file (pain.001) is created for online banking.
3. When the payment is **Succeeded** (manually or via an imported bank message CAMT.054), Tryton posts *payable to 2749 Bankbewegung* in the journal Zahlungsverkehr; the supplier item is reconciled.
4. With the **bank statement** (e.g. CAMT.053) the statement line is matched to the payment or payment group: *2749 Bankbewegung to bank* – the transit account is reconciled again.

### Direct Debit (Incoming)

1. **Create direct debits** for due rent receivables of tenants with a valid mandate.
2. **Approve** and **process** the payments: SEPA file pain.008 for the bank.
3. On **Succeeded** Tryton posts *2749 Bankbewegung to rent receivable* (e.g. 2000).
4. The **bank statement** posts *bank to 2749 Bankbewegung*.

A **returned direct debit** is entered by setting the payment to **Failed**: Tryton cancels the transit posting, the receivable is open again.

## Manual Payments

## Payment Run and Direct Debit

## Bank Statement
