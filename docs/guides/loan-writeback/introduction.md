---
title: "Loan writeback build guide"
sidebar_label: "Introduction"
description: "Lend with Codat and our Assess product" 
displayed_sidebar: "docs"
---

:::tip Who is this guide for?

This guide is for tech-savvy backend developers who know how to use an API. No frontend experience is needed.

:::

WE ASSUME YOU ARE MOVING ON HERE FROM THE LOAN QUALIFICATION GUIDE - YOU CAN EVEN USE THE SAME COMPANY FOR THIS 

### Summary

🎯 Codat makes lending easier by getting you trusted data with which to check a loan applicant’s finances. With our demo app, you will experience the end-to-end lending process flow with automatic decision-making supported by Codat's [Assess](/assess/overview) product. We will focus on the lender's perspective. 

⏳ Estimated time to complete: 20-30 minutes

🛠️ The demo project is implemented in [.NET 7.0](https://dotnet.microsoft.com/en-us/download/dotnet/7.0) as a backend API. You can configure and run the demo app in the terminal, or use your preferred IDE or code editor.

Why loan writeback
There are numerous ways in which a bookkeeper can account for a loan in an accounting platform. Depending on the approach it can make it hard for SMBs to close their accounts when done incorrectly. It has been known that some bookkeepers register a loan as a direct income or even an invoice in their accounting platform. This can result into loans being registered as revenue and repayments as an operating cost therefore making it hard for the bookkeeper to close a reporting period or close their books at the end of the year. This tutorial acts as a best practice in how to correctly account for a loan in an accounting platform programmatically. 

What is loan writeback?
Loan writeback is the process of updating the accounting platform with information on the loan, this includes: 

Name of lender (defined as a supplier from Codat’s data types)

Bank accounts:

Business bank account – the borrowers bank account where the money is deposited.

Lenders bank account – this is a virtual account used to deposit the loan (should have a negative balance).

Nominal expenses account to record fees and interest.

Supported platforms (Xero only)
According to invoice finance implementations guide the supported platforms are as follows

Xero

QuickBooks Online (QBO)

Exact (Netherlands)

Oracle Netsuite

Sage Business Cloud

For simplicity we should only discuss Xero but with a call to action to try with QBO(?) – don’t know if we can reliably do this for QBO.

Key assumptions
The following assumptions allow us to connect an accounting platform without knowledge of the platforms setup:

The lender has never provided a loan to the SMB before.

The lender is offering a fixed term loan.

The borrower has already connected an accounting platform (Xero) to the company before loan writeback. 

The borrower has a bank account in the accounting platform.

A single draw down is made (i.e. one payment into the borrowers account).

A fee/interest is applied to the loan.

---

### Read next

Now that you know the focus and purpose of our demo app and its guide:
* [Set up Codat and your local environment](/guides/loan-qualification/setting-up).
