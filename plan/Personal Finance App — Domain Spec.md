# Personal Finance App — Domain Spec

Nov 20, 2026 · @Eduardo

This spec defines the domain rules for a manual, multi-currency, bucket-based personal finance app. It is built around one proven workflow first, then generalized for others. Technical architecture is deliberately out of scope until these rules are settled.

## Vision and principles

- **Personal first, generalizable second.** Model the owner's real weekly workflow, then turn its habits into configurable rules others can adopt.
- **Open source and community-led.** Open-source foundation; the community suggests features and the ones with strong approval get built. No monetization for now.
- **The app never moves money or changes a budget on its own.** It reminds, calculates and suggests; the user confirms every allocation, expense and budget change.
- **Self-contained, manual inputs.** No bank feeds or outside data in v1. Everything is entered in the app.
- **Plan conservatively.** Plan around minimum expected income, pessimistic exchange rates and estimated bills, so buckets are always covered.
- **Low cognitive overhead.** Don't track every small expense. Plan periodic costs ahead as subsections; untracked everyday spending shows up as the remainder.

## Core concepts

Money is planned in **subsections** inside single-currency **buckets**, and held in physical **locations** that are reconciled against reality.

| Concept | What it is | Key attributes |
| --- | --- | --- |
| Income Source | A stream of money coming in (a salary, rental income in another currency) | Currency; planning cadence (weekly, bi-weekly, monthly, yearly); minimum expected amount; arrival: fixed or irregular |
| Bucket | A logical pool of money in one currency (a home-currency bucket, a foreign-currency bucket) | Currency; its locations; its subsections |
| Location | A physical place where bucket money sits (a bank account, cash) | Bucket; actual balance at last reconciliation |
| Subsection | A division of a bucket that money is allocated to | Type (obligatory or goal); behavior (accumulating or pass-through); priority; target amount and date; payment currency |
| Line Item | An estimated cost inside a subsection (rent, electricity, Netflix) | Estimate; cadence; payment currency |
| Entry | Any recorded movement of money | Kind: income, allocation, expense, transfer between locations, currency conversion, self-loan or repayment |
| Self-Loan | Money borrowed from one subsection or bucket to cover another | Lender; borrower; amount; repayment plan; balance remaining |
| Reconciliation Snapshot | Actual balances of every location at a point in time | Date; balance per location; mismatch flagged |
| Household / Member | Who owns the data and who can see or edit it (later) | Members; roles (owner, viewer) |

**Subsection types**

- **Obligatory / scheduled:** required bills and deadlines (rent or mortgage, annual property tax, health and life insurance). Tracked as target amounts by target dates.
- **Savings / goals:** self-directed targets and planned perks (house down payment, a family event fund, a monthly night out, vacations, charitable giving). Same tracking, with flexible pressure.

**Subsection behaviors**

- **Accumulating:** builds toward a target, and keeps any surplus (most subsections).
- **Pass-through:** funded and fully paid out every cycle (a partner's weekly allowance, discretionary credit-card spending).

Debts sit as their own subsection or as a line item inside one (a $35/month laptop payment split into weekly allocations). Rental property maintenance and operating costs are a dedicated subsection in the relevant bucket.

## The allocation loop

Each income source runs its own allocation loop on a fixed planning cadence, even when the money arrives on irregular dates.

1. **Reminder.** The app reminds the user that a planning period is due (weekly paycheck, monthly rent).
2. **Log the income.** The user enters the actual amount received and the date it arrived. Both are editable.
3. **Allocate.** The app proposes the planned amount for each subsection, in priority order, based on the *minimum expected income*. The user confirms or adjusts each one: more into a subsection, or less when needed.
4. **Remainder.** Whatever is not allocated stays in the bucket as the remainder pool for everyday spending (groceries, gas).
5. **Move the money.** The user moves the money into its physical locations and records any transfers.
6. **Reconcile.** The user enters actual location balances; the app compares them with the bucket's expected total and flags any mismatch.

**Two dates on every income entry**

- **Received date:** when the money actually arrived. It updates balances and can be allocated right away.
- **Planning date:** the scheduled date the planning cadence assigns it to. Forward-looking plans count the money only from this date.

Example: a $3,000 goal over five months starts December 1. A tenant pays early on November 20. The deposit is logged and allocated on November 20, but the goal's timeline does not move earlier.

**Cadence math.** Monthly targets convert to weekly contributions at 4 weeks per month (default, builds a buffer) or 4.3 weeks per month (configurable). Yearly or dated targets divide by the periods left until the target date.

## Expenses and variance

Every planned value has an estimate and an actual; the app records the difference and the user decides what to do with it.

**Logging an expense**

1. The app reminds the user that a scheduled expense is due.
2. The user pays it from its physical location and logs the actual amount (and actual exchange rate, if another currency).
3. The app deducts it from the subsection and compares it with the estimate.

**Where estimate vs. actual applies**

| Case | Estimate | Actual |
| --- | --- | --- |
| Variable bill | Budgeted electricity, e.g. $150 | Bill comes in at $170 (+$20) or $120 (−$30) |
| Currency conversion | Pessimistic rate used when saving | Rate on the day of payment |
| Cross-currency self-loan repayment | Repayment plan set up front | Amount actually moved or received |

**What the app shows**

- **Ahead of target:** a subsection holds more than its plan requires (from the 4-week buffer or a lower bill). The surplus stays in that subsection.
- **Behind target:** a subsection will not reach its target by its date at the current contribution.
- **Suggestions only:** the app may suggest adjusting future contributions or estimates (later from historical bills and rates). The user approves and edits the budget manually.

## Rules

**Priorities**

- Obligatory subsections are funded first by default.
- The user can rank any subsection manually (an emergency fund above a vacation goal, even though neither is a bill).
- When income comes in light, subsections are funded in priority order until the money runs out.

**Flexible contributions**

- The user can put extra money into any subsection, to spend or to save, or contribute less in a given period.
- Income above the minimum expected goes wherever the user directs it.

**Unexpected income**

Money outside any income source's plan is logged as an **unexpected inflow**, and the user chooses where it goes:

- **Top up a subsection:** the extra is flagged as a surplus in that subsection.
- **Repay a self-loan:** it reduces the loan's remaining balance.
- **Spend it directly:** optionally logged as an expense in a subsection, if the user wants to track it there.

**Credit cards**

- **Self-imposed cap:** an internal limit below the bank's, sized to be paid off in 1 to 4 weeks (e.g. a $300 cap at \~$100/week).
- **Fixed bills on a card:** paid from that bill's own subsection.
- **Discretionary spending on a card:** covered by a dedicated pass-through subsection.
- **Exceptions** (e.g. a second card kept for eating out or emergencies) are tracked explicitly; recurring items should move into subsections over time.

**Self-loans**

- Borrow from a specific subsection, spread across savings, or from a **different bucket**.
- Repaid by temporarily increasing the borrower's contribution until the balance reaches zero.
- **Cross-currency loans:** the repayment plan is set as accurately as possible up front; the amount actually moved or received is what reduces the loan balance.

**Currencies**

- Each bucket has exactly one currency.
- A subsection's **payment currency** may differ from its bucket's currency (a bill charged in a foreign currency, funded from a home-currency bucket).
- Exchange rates are entered manually. Planning uses a conservative, pessimistic rate; the actual rate is logged at payment.

**Split funding**

One expense can be covered from several buckets through configuration, with no special feature. Example: a mortgage funded 40% from a subsection in a foreign-currency bucket and 60% from a subsection in a home-currency bucket. It is fully covered when both are funded.

**Partner allowance**

A pass-through subsection with a weekly amount, funded and marked as given each cycle.

## Reconciliation and history

Reconciliation checks that the money the plan says exists is really there.

- The user enters the actual balance of each location (bank account, cash) in a bucket.
- Expected bucket total = income received − expenses logged ± transfers, conversions and self-loans.
- If the sum of location balances differs from the expected total, the app flags it ("something doesn't add up") and shows the difference.
- Untracked everyday spending from the remainder pool shows up here as the gap, not as individual expenses.

**History**

The app keeps a permanent log of every income entry, allocation, expense, transfer, conversion and self-loan, plus every reconciliation snapshot. This supports reports (shareable with a partner), "did I hit my targets" reviews and later estimate suggestions.

## Scope

Version 1 is a single-user app that runs the full allocation, expense and reconciliation loop by hand.

| Area | Version 1 | Later | Out of scope |
| --- | --- | --- | --- |
| Users | One user | Household with members and roles (partner as viewer); data owned by a household from day one |  |
| Income | Multiple sources and currencies, fixed cadences, received vs. planning date, minimum expected income |  | Bank feeds and outside data |
| Buckets and subsections | Buckets, locations, subsections, line items, types, behaviors, priorities |  |  |
| Expenses | Reminders, manual logging, estimate vs. actual, ahead/behind flags | Suggested estimates from historical bills |  |
| Currencies | Manual exchange rates, payment currency per subsection | Historical exchange-rate insights | Live rate feeds |
| Self-loans | Within and across buckets, repayment plans |  |  |
| Credit cards | Caps, fixed vs. discretionary flows |  |  |
| Reconciliation and history | Snapshots, mismatch flags, full log | Reports to share |  |
| Habits |  | Weekly habit and cadence module (to be defined) |  |
| Community | Open-source repo | Feature voting | Monetization (for now) |
| Taxes |  |  | Handled with an accountant |

## Open questions

- [ ] Weekly habit module: how to frame the mindset and routine side (e.g. a fixed weekly eating-out schedule).
- [ ] Accumulating buffers: should a subsection's surplus ever be capped, or only flagged for the user to decide?
- [ ] Cross-bucket self-loans: is the loan balance tracked in the lender's currency or the borrower's?
- [ ] Open-source license: permissive (MIT) or copyleft (AGPL)?
