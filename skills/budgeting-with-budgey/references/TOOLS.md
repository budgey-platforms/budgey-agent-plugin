# Budgey MCP tool reference

Every tool the Budgey MCP server exposes, grouped by task. Descriptions
come from the server; call the tool for its full input schema.

## Orientation

- `getConnectionInfo` — Describe this connection: user, plan and limits, active budget and period, and other available budgets. Call first.

## Reading the budget

- `getBudgetSummary` — Get a summary of the current budget period including total budgeted, total spent, and remaining for each category
- `getCurrentPeriodStatus` — Get detailed info about the current period's progress including days elapsed, days remaining, percent complete, and spending status.
- `getBudgetPeriodInfo` — Get information about budget periods including current period dates, next period dates, pay frequency, and when the next period starts. Use this when users ask about t…
- `getBudgetPeriodHistory` — Get the last N budget periods with dates, spending totals, and status. Useful for understanding spending patterns and period history.
- `getCategories` — Get a list of all categories with their budgeted amounts and current spending for the budget period
- `getCustomAccountTypes` — Get the list of custom account types for this budget. Use when user mentions a payment method that might be a custom account.

## Transactions

- `getTransactions` — Get a list of transactions for the current budget period. Can optionally filter by category, type, or date range.
- `searchTransactions` — Search transactions across all budget periods by name, category, date range, or amount. More flexible than getTransactions which only searches the current period.
- `addTransaction` — Add a new transaction. If user provides a verbose description like "I spent $50 at Whole Foods on groceries using my Chase card", extract: - name: Clean merchant/descr…
- `updateTransaction` — Update an existing transaction: name, amount, type, date, note, account type, or goal link. You must provide the transaction ID and category ID.
- `deleteTransaction` — Delete a transaction by its ID and category ID.
- `splitTransaction` — Split one transaction across categories ("$60 at Target was $40 groceries and $20 kids"). Creates child transactions replacing the original; the split amounts must sum…
- `batchAddTransactions` — Create many transactions in one call. Prefer this over sequential addTransaction for lists.
- `parseAndAddFromText` — Parse freeform multi-line spend text into structured transactions (does not write; pair with batchAddTransactions).
- `importTransactions` — Import multiple transactions at once from a CSV or Excel file. Use this when a user uploads a bank statement or transaction export. Each transaction needs a descriptio…
- `undoImport` — Undo a bulk import: delete every transaction created by one importTransactions call. Use when the user asks to undo, revert, or delete an import (e.g. 'undo that impor…
- `findDuplicateTransactions` — Detect likely duplicate transactions (same amount/name/day). Optionally delete extras.
- `getFrequentTransactions` — Find transactions that repeat frequently (same store/name). Useful for identifying spending patterns, subscriptions, and regular purchases.

## Categories

- `createCategory` — Create a new budget category with a name, amount, and optional color. By default the current period's total grows by the amount so the period stays balanced; set fitWi…
- `updateCategory` — Update a category's budgeted amount, name, or color. `total` is an ABSOLUTE value the user stated — never guess it, and never use this to move/transfer money between c…
- `deleteCategory` — Permanently delete a category AND every transaction in it (all periods). Irreversible and destructive — only call after the user explicitly confirms they understand th…
- `updateBudgetPeriodCategoryTotal` — Set ONE category's budgeted amount for the current period to an exact value the user stated (\"make Groceries $300\"). newTotal is ABSOLUTE — read the current value fi…
- `updatePeriodAllocations` — Set or hide category budgets for THIS period only (allocations[]). Totals are ABSOLUTE values the user stated — never guess them, and never use this to move/transfer m…
- `rescalePeriodAllocations` — Proportionally rescale this period's category allocations to sum to expectedIncome (Auto-fit).
- `rebalanceCategories` — THE tool for transferring/moving money between categories: "move $16 from Groceries to Eat Out", "transfer $50 to Savings". Takes the amount to MOVE — it computes the…
- `categorizeUncategorized` — Find uncategorized or Other transactions and propose/apply categories.

## Analysis & insights

- `getSpendingAnalysis` — Analyze spending patterns across multiple budget periods. Returns trends, category comparisons, and spending insights.
- `getSpendingByPeriod` — Get spending totals for each budget period. Useful for tracking spending trends over time and comparing periods.
- `getTransactionStats` — Get overall transaction statistics including totals, averages, and top categories/stores. Can optionally filter to a specific budget period.
- `generateChartData` — Generate data formatted for displaying charts. Supports pie charts (category breakdown), bar charts (period comparison), and line charts (spending over time).
- `getBudgetHealthInsights` — Get insights about budget health including overspending alerts, savings opportunities, and personalized recommendations.
- `listSpendingByAccount` — Spending breakdown by account type for the current period.
- `runBudgetAudit` — One-shot health check of the current period: paycheck, allocation drift, overspent categories, inbox, goals. Returns prioritized issues with suggested tools.
- `whatNeedsAttention` — Short proactive digest: overspend, period ending, inbox, goals, auto-period paycheck, allocation drift.
- `simulateScenario` — What-if without writing: apply hypothetical expenses/income/category changes and report before/after.
- `draftWeeklyReview` — Generate a structured week-in-review narrative and metrics.

## Budget periods & pay schedule

- `createBudgetPeriod` — Create a new budget period with start and end dates. Optionally set expectedIncome (paycheck) and rescaleAllocations to fit categories to the paycheck.
- `createNextBudgetPeriod` — Automatically create the next budget period based on the pay schedule. No dates required - calculates from the most recent period and pay frequency. Accepts optional e…
- `previewNextPeriod` — Preview what the next budget period would look like without creating it. Shows dates, suggestedIncome (paycheck prefill), and category allocations that would be copied.
- `updateBudgetPeriod` — Change a budget period's start/end dates (e.g. to fix a mistyped range). Does not touch allocations or income — use setPeriodExpectedIncome / setPeriodAllocations for…
- `deleteBudgetPeriod` — Permanently delete a budget period AND every transaction recorded in it. Irreversible and destructive — only call after the user explicitly confirms, and never for the…
- `closePeriodAndRollover` — Create the next period after the current one (rollover). Uses expectedIncome and optional rescale. Prefer when autoCreatePeriods is off.
- `setPaySchedule` — Update budget pay frequency / anchor / semi-monthly days / autoCreatePeriods.
- `setAutoCreatePeriods` — Toggle automatic budget period creation (premium). Same as Settings → pay schedule automatic periods.
- `setCarryOverMode` — Set envelope rollover for future periods (premium). OFF = fresh start each period. ADD = each new period gets its normal budget plus the prior period\'s leftover per c…
- `getPeriodIncome` — Read this period's paycheck (expectedIncome), whether it was auto-created, allocation sum, and unallocated drift vs categories.
- `setPeriodExpectedIncome` — Set or clear this period's paycheck (expectedIncome). Optionally rescale category allocations to match.
- `confirmAutoPeriodPaycheck` — Confirm or set paycheck on an auto-created period (twin of AutoPeriodIncomeNudge). Omit expectedIncome to accept the carried value.
- `planPayday` — Given this/next period's paycheck, propose bills, category targets, and leftover. Does not write unless user executes later.

## Goals

- `getGoals` — Get all savings and debt goals for the current budget with their progress and contribution history
- `createGoal` — Create a new savings or debt goal. SAVINGS goals track money you want to save. DEBT goals track money you owe and want to pay off.
- `updateGoal` — Update a goal's details: name, target amount, target date, status, pin, streak frequency, and for DEBT goals the interest rate and monthly payment day. Note: setting s…
- `deleteGoal` — Permanently delete a goal and its contribution history. Irreversible — only call after the user explicitly confirms deletion. If they might want the history later, sug…
- `addGoalContribution` — Add a contribution (payment) to a goal. For savings goals, this adds to the saved amount. For debt goals, this records a payment.

## Recurring bills

- `getRecurringTransactions` — Get all recurring transaction templates for the budget. These are transactions that repeat on a schedule.
- `addRecurringTransaction` — Create a new recurring transaction template. The transaction will automatically appear based on the frequency.
- `updateRecurringTransaction` — Update an existing recurring transaction template. Only the fields provided will be updated.
- `deleteRecurringTransaction` — Delete a recurring transaction template. The recurring transaction will no longer generate future transactions.

## Bank sync (Plaid)

- `listBankConnections` — List connected bank institutions and status.
- `getBankConnectionHealth` — Get health/status for one or all bank connections.
- `syncBankConnections` — Trigger Plaid transactions sync for linked bank items (same as POST /api/plaid/items/:id/sync). Premium-gated.
- `getPlaidInbox` — List pending bank-sync review inbox items for the user.
- `reviewInboxItem` — Import or ignore a single Plaid inbox item.
- `bulkReviewInbox` — Import or ignore many inbox items in one call.

## Accounts, sharing & setup

- `createCustomAccountType` — Create a custom account type (cash/credit bucket) for the budget.
- `updateCustomAccountType` — Update a custom account type's name, icon, color, or scope. Global types (shown in all the user's budgets) can only be edited by their creator; `global` toggles the sc…
- `deleteCustomAccountType` — Delete a custom account type. Only allowed when no transactions or recurring transactions use it (suggest reassigning them first). Global types can only be deleted by…
- `renameBudget` — Rename the current budget.
- `shareBudgetInvite` — Invite someone to a shared budget by email (creates invite token + sends email when Resend is configured). Confirm email with user first.
- `listInvitations` — List pending invitations for this budget.
- `manageNotes` — List/create/update/delete period notes (SharedNotes).
- `setSpendingGuardrail` — Persist a soft spending guardrail as a user memory for future audits.

## Memory

- `rememberFact` — Remember an important fact about the user for future conversations. Use this when the user explicitly asks you to remember something, or when they share significant pe…
- `recallMemories` — Search through your memories about the user to find relevant information. Use this to recall facts the user has previously shared.
- `forgetMemory` — Remove a specific memory when the user asks you to forget something. Use this when the user explicitly requests to forget information.
- `whatDoYouKnow` — List what you remember about the user. Use this when the user asks "what do you know about me?" or similar questions.

## Multi-step planning

- `proposePlan` — Return a structured multi-step plan (tool names + args + human labels) for a user goal. Does not execute.
- `executePlan` — Run approved plan steps in order by invoking named tools available in this session. Stops on first failure. Prefer dryRun to preview.
- `verifyLastActions` — Re-fetch key period numbers and check structured expectations after mutations.

## Misc

- `calculateTip` — Calculate tip amounts for a restaurant bill. Use when user mentions a meal, dinner, restaurant bill and wants tip help. Returns 15%, 18%, 20% options with recommendati…
- `navigateApp` — Navigate the user to a screen in the Budgey app. Use ONLY when the user explicitly asks to open, show, go to, or take them to a place in the app (e.g. "show me my tran…

