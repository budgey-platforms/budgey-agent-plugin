---
name: budgeting-with-budgey
description: How to work with a user's Budgey budget through the Budgey MCP tools — the paycheck-period model, choosing the right read tool, safe mutation workflows, multi-budget accounts, and free-plan limits. Use whenever reading or changing anything in Budgey (budgets, transactions, categories, goals, recurring bills).
license: MIT
metadata:
  author: budgey-platforms
  version: "1.1"
---

# Budgeting with Budgey

Budgey is a paycheck-based budgeting app. Money is planned per **budget
period** (a paycheck cycle — e.g. the 7th to the 21st), NOT per calendar
month. Every amount you read or write belongs to the user's active budget and
its current period.

## Start every session

Call `getConnectionInfo` first. It returns the authenticated user, their plan
and its limits, the active budget, the active period's dates, and any other
budgets on the account. Do not assume a monthly cycle — period dates come
from the user's pay schedule.

The server exposes a large tool surface (80+ tools). If you need something
not described here, look for it by name rather than assuming it's missing.

## Core concepts

- **Remaining this period** is the number users care about: what's safe to
  spend right now. Prefer it over raw totals when summarizing.
- **Categories** hold an allocation per period; spending draws it down. Some
  budgets carry leftover balances into the next period (envelope-style
  carryover) — treat `carriedIn` as part of the available amount.
- **Periods roll over automatically** for opted-in budgets, and the next
  period is created a couple of days before the current one ends. So a
  future period may already exist. When logging a transaction near a
  boundary, make sure it lands in the period whose dates contain the
  transaction date.
- Amounts are plain numbers in the user's currency (42.50, not "$42.50").

## Choosing the right read tool

- `getBudgetSummary` — overall state of the active budget and period.
- `getCurrentPeriodStatus` — where the user stands in the current period.
- `getTransactions` — transactions in the **current period only**.
- `searchTransactions` — searches **all periods** by name, category, date
  range, or amount. Use this for "how much did I spend on X last month".
- `getSpendingAnalysis` — aggregated spending breakdowns.

## Safe workflow

1. Read before you write: `getBudgetSummary`, `getTransactions`, or
   `getSpendingAnalysis` before mutating anything.
2. Confirm with the user before deleting anything. Deletions
   (`deleteTransaction`, `deleteCategory`, `deleteGoal`, …) are permanent.
3. Bulk imports via `importTransactions` can be reversed with `undoImport`,
   but only for the most recent import — so verify the result immediately
   and tell the user if it looks wrong.
4. When the user asks "can I afford X?", answer from the category's
   remaining amount this period, and say which period you used.

## Accounts with more than one budget

`getConnectionInfo` lists `otherBudgets`. All tools act on the active budget
only. To operate on a different one, set the `X-Budgey-Budget-Id` header on
the MCP connection to that budget's id — there is no tool that switches
budgets mid-session.

## Free-plan limits

Free budgets cap transactions per month, categories per budget, budget
periods, and active goals; recurring transactions are paid-only. Tools return
an upgrade message when a limit is hit. Relay that message to the user
plainly and do not retry — retrying will fail the same way.
