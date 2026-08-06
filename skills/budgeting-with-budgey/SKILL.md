---
name: budgeting-with-budgey
description: How to work with a user's Budgey budget through the Budgey MCP tools — paycheck-period model, safe tool workflows, and free-plan limits. Use whenever reading or changing anything in Budgey (budgets, transactions, categories, goals, recurring bills).
---

# Budgeting with Budgey

Budgey is a paycheck-based budgeting app. Money is planned per **budget
period** (a paycheck cycle — e.g. the 7th to the 21st), NOT per calendar
month. Every amount you read or write belongs to the user's active budget and
its current period.

## Start every session

Call `getConnectionInfo` first. It tells you which budget and period are
active, the user's currency, and their plan. Do not assume a monthly cycle —
period dates come from the user's pay schedule.

## Core concepts

- **Remaining this period** is the number users care about: what's safe to
  spend right now. Prefer it over raw totals when summarizing.
- **Categories** hold an allocation per period; spending draws it down. Some
  budgets carry leftover balances into the next period (envelope-style
  carryover) — treat `carriedIn` as part of the available amount.
- **Periods roll over automatically** for opted-in budgets; the next period
  may already exist a couple of days before the current one ends. When
  logging a transaction near a boundary, make sure it lands in the period
  whose dates contain the transaction date.
- Amounts are plain numbers in the user's currency (42.50, not "$42.50").

## Safe workflow

1. Read before you write: `getBudgetSummary`, `getTransactions`, or
   `getSpendingAnalysis` before mutating anything.
2. Confirm with the user before deleting or bulk-importing anything.
   Deletions are permanent; imports can be undone only via the undo tool
   immediately afterwards.
3. When the user asks "can I afford X?", answer from the category's
   remaining amount this period, and say which period you used.

## Free-plan limits

Free budgets cap transactions per month, categories per budget, and stored
periods. If a mutating tool returns a limit error, tell the user plainly
which limit they hit — do not retry.
