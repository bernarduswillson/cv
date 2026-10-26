# Kick Avenue - Customer reviews speaker notes

Analysis snapshot: **October 26, 2026**. All analysis timestamps use **Asia/Jakarta**.

These notes explain the first Kick Avenue bullet in the [CV source](../../src/experience.tex). The percentage is an exploratory, observational comparison. It is not an experimentally measured increase caused by the feature.

## Current CV wording

> Owned end-to-end delivery of customer reviews, ratings, and photo uploads across backend, web, and mobile, with review-engaged shoppers showing 63% higher observed first-purchase conversion across six weekly cohorts.

## Short interview explanation

> I owned the implementation across the backend, web, and mobile apps. To examine customer behavior, we connected tracked mobile review-screen activity with paid purchase records. In six weekly shopper cohorts, people who opened reviews during the first hour after their initial observed product view had a 6.2% first-purchase conversion rate, compared with 3.8% for the comparison group. That is approximately 63% higher in relative terms. This was an observational analysis, so I would not claim that the feature itself caused a 63% increase. The result depends on the cohort definition and the measurement window.

This retrospective analysis was prepared while strengthening the CV. It was not a pre-existing company experiment or an experiment run during implementation.

## What the 63% means

| Group | Eligible accounts | First paid buyers | Conversion rate |
| --- | ---: | ---: | ---: |
| Opened a review list or review-photo screen within the first hour | 305 | 19 | 6.2295% |
| Did not open those screens within the first hour | 8,245 | 315 | 3.8205% |

The calculation uses the unrounded rates:

```text
Review-group conversion     = 19 / 305             = 0.06229508197
Comparison-group conversion = 315 / 8,245          = 0.03820497271
Relative difference         = (0.06229508197 / 0.03820497271 - 1) × 100
                            = 63.0549%
                            ≈ 63% higher observed conversion
```

The absolute difference is **2.4090 percentage points**, and the ratio is approximately **1.63 times**. The claim does not mean that 63% of review readers purchased, or that the feature added 63 percentage points to conversion.

## Why use a one-hour window?

The hour was an analysis choice, not a GA4 rule, a product requirement, or a duration established as optimal for Kick Avenue.

The analysis first classified whether an account opened reviews, then measured subsequent purchases. This makes the sequence explicit and avoids assigning a shopper to the review group based on activity occurring after an outcome being measured. It does not eliminate differences in shopping intent between groups.

The one-hour choice was not formally preregistered or experimentally validated. It should be explained whenever the percentage is discussed.

### Sensitivity to that choice

| Classification window | Review group: buyers / accounts | Comparison group: buyers / accounts | Review conversion | Comparison conversion | Relative difference |
| --- | ---: | ---: | ---: | ---: | ---: |
| 15 minutes | 23 / 284 | 368 / 8,330 | 8.10% | 4.42% | 83.3% higher |
| 30 minutes | 25 / 303 | 339 / 8,279 | 8.25% | 4.09% | 101.5% higher |
| 60 minutes | 19 / 305 | 315 / 8,245 | 6.23% | 3.82% | 63.1% higher |

Changing the window changes group membership, the follow-up start time, and eligibility. For example, an account that pays before the one-hour boundary is excluded from that comparison. These are related analyses with different denominators, not three measurements of an identical population.

The varying results show why 63% should not be presented as a universal effect size. Do not select the largest percentage as a replacement CV claim.

## Exact cohort definition

The index dates were six Mondays:

- September 7, 14, 21, and 28, 2026.
- October 5 and 12, 2026.

These were sampled days across six weeks, not every shopper or every day in those weeks. This broader sample followed an initial three-day exploratory comparison. The initial result of approximately 2x was not retained as the headline result after expanding the sample.

For each identifiable account:

1. Find its earliest mobile `view_item` event across the six selected dates. Call that time `t0`. This is not necessarily the person's first-ever product view or the start of a session.
2. Set the classification boundary to `t1 = t0 + 60 minutes`.
3. Exclude staff accounts, accounts with a recorded paid purchase at or before `t1`, and accounts with a recorded review-screen view during the seven days before `t0`.
4. Exclude accounts with ambiguous platform assignment, including accounts observed on both iOS and Android on their index day.
5. Assign accounts with a qualifying review-screen view in `[t0, t1)` to the review group. Assign the remaining eligible accounts to the comparison group.
6. Keep those assignments fixed. An account that opens reviews later remains in the comparison group for this analysis.
7. Observe qualifying first paid purchases from one minute after `t1` through seven days after `t1`. Require both sale creation and payment to be at least one minute after `t1`.
8. Count each account once. A first paid purchase must match that account's earliest recorded `customer_paid` across its historical sales, not just its first purchase within the selected dates.

The minute buffer reflects the precision of GA4's `dateHourMinute` dimension. Purchases in the buffer are not counted as outcomes. The latest index date leaves enough follow-up before the October 25 purchase cutoff.

### Qualifying purchase rules

- Use production `sale.customer_paid` and `sale.created_at`, rather than treating a GA4 `purchase` event as payment confirmation.
- Include direct `WEB`, `APP`, `ANDROID`, and `IOS` sales with `offer.franchise IS NULL`.
- Exclude `CANCELLED`, `REFUNDED`, and `KA_RETURNED_SELLER` outcomes.
- Determine prior paid purchase history across all recorded sales and channels, including records that are excluded from the current outcome totals. This is deliberately conservative about calling someone a first-time buyer.
- Identify staff through administrative and internal roles, including admin, administrator, superadministrator, superadministratorx, internal, customer_service, cashier, inventory_admin, legit_check, sale_supervisor, supervisor, admin_express_stock, blog_admin, and courier.

## Data sources and implementation evidence

| Evidence | Source | Use |
| --- | --- | --- |
| Product interest | GA4 `view_item`, mobile platforms | Establish `t0` |
| Review engagement | GA4 `screen_view` on `product-reviews` or `product-review-photos` | Classify review exposure |
| Account matching | GA4 `customUser:id` | Match numeric account IDs to production records |
| Payment and first-buyer history | Production `sale` records, joined to `offer` | Confirm paid outcomes and earliest paid purchase |
| Staff exclusions | Production `role_user` joined to `role` | Remove internal accounts |

The analytics source was Kick Avenue's **Web and Mobile** GA4 property. Retained extracts for this comparison reported no sampling, row loss, or truncation. This does not establish that event tracking captured every real interaction.

Relevant implementation paths inspected during the investigation:

- `fe-marketplace-flutter/lib/src/utils/app_analytic.dart`: account-ID user property, GA4 events, and the distinction between order creation and confirmed payment.
- `fe-marketplace-flutter/lib/src/routing/product_routes.dart`: review-list and review-photo screen names.
- `be-api-laravel/app/Traits/CanProcessPaymentTrait.php`: paid sale creation and the buyer-account association.
- `be-api-laravel/app/Models/Sale.php`: direct Kick Avenue channel and franchise definitions.

The implementation scope spans backend, web, and mobile. **The exposure measurement here covers identifiable mobile review-screen viewers**, not every user of all three platforms. Paid outcomes can occur on a direct web or app channel.

## Platform and date check

Grouping accounts by index date and platform produced an adjusted rate ratio of approximately **1.662**, compared with the unadjusted **1.631**. This check addresses measured platform and date differences, not unmeasured intent or other confounders.

For each date/platform group, let `a` and `n1` be review-group buyers and accounts, and `c` and `n0` be comparison-group buyers and accounts. The aggregate adjustment used:

```text
Adjusted ratio = Σ[a × n0 / (n1 + n0)] / Σ[c × n1 / (n1 + n0)]
```

The [aggregate snapshot](data/kick-avenue-reviews-2026-10-26.json) includes the underlying date/platform counts for this calculation.

## Limitations to explain honestly

- **Association, not causation.** Shoppers who choose to read reviews may already have stronger purchase intent. There was no randomized assignment.
- **Small review outcome count.** The headline review group contains only 19 first purchases. The approximate, unadjusted 95% interval for the rate ratio is wide, about 1.04 to 2.55, and does not account for all exploratory analysis choices or confounding.
- **Window dependence.** The headline depends on the one-hour classification rule and seven-day follow-up.
- **Sampled dates.** Six Mondays do not establish a result for every weekday or the full customer population.
- **Later exposure.** Comparison-group accounts may open reviews after their classification window.
- **Tracking and identity coverage.** Anonymous or untracked activity is excluded. First-time means first recorded paid purchase under the matched account, not necessarily the person's first purchase under any identity.
- **Product attribution.** Review-screen events do not identify the reviewed product, so this analysis cannot establish that the same product was subsequently purchased.
- **Changing order state.** Outcomes use statuses observed on October 26. Later refunds or corrections can change the result.

## How the other numbers discussed differ

| Number | Definition | Why it differs from 63% |
| --- | --- | --- |
| 13.5% | 468 of 3,476 identifiable review viewers with no prior paid purchase bought within seven days of their first recorded review view | A conversion rate within a review-viewer cohort, with no comparison group |
| 500 first paid buyers | Accounts whose first recorded paid purchase followed any qualifying review view within the preceding seven days | Allows a later review visit to qualify, rather than looking only at the first review visit |
| 63% higher | Relative comparison of 19 / 305 with 315 / 8,245 | A different population and a fixed one-hour classification boundary |

The first-view analysis used tracked review views from August 27 through October 18, with purchases observed through October 25. These dates describe the extraction window, not a confirmed feature release date. These metrics must not be combined or presented as interchangeable measures.

## Interview follow-up answers

**Did you increase conversion by 63%?**

> We observed a 63% relative difference between the defined groups. We did not run an experiment that establishes a 63% causal increase. That is why the CV says "observed."

**Why not use the approximately 102% result from the 30-minute window?**

> That is a different cohort definition. Selecting the largest result would overstate how stable the finding is. The sensitivity checks are part of the explanation, not interchangeable headline claims.

**What would establish a causal impact?**

> A prospectively designed experiment with randomized review visibility, a defined first-purchase outcome, a fixed observation window, and sufficient sample size would provide stronger evidence. It should account for product availability, channel, promotions, and tracking coverage.

**What claim is less dependent on the comparison design?**

> Review engagement preceded 500 customers' first recorded paid purchases within a seven-day lookback. That is still a descriptive association, but it avoids interpreting a chosen exposure window as a general conversion uplift.

## Recalculate the headline from the saved counts

Run this from the repository root. It uses only the aggregate snapshot and Python's standard library.

```sh
python3 - <<'PY'
import json
from pathlib import Path

path = Path("docs/speaker-notes/data/kick-avenue-reviews-2026-10-26.json")
data = json.loads(path.read_text())
counts = data["windows"]["60min"]["totals"]
review_rate = counts["review_first_buyers"] / counts["review_accounts"]
comparison_rate = counts["other_first_buyers"] / counts["other_accounts"]
print(f"Review conversion: {review_rate:.4%}")
print(f"Comparison conversion: {comparison_rate:.4%}")
print(f"Relative difference: {(review_rate / comparison_rate - 1) * 100:.4f}%")
print(f"Absolute difference: {(review_rate - comparison_rate) * 100:.4f} percentage points")
PY
```

Expected relative difference: **63.0549%**. Expected absolute difference: **2.4090 percentage points**.

The snapshot supports recalculating aggregate arithmetic, not rebuilding the original cohorts. Account-level extracts were removed after analysis and are not included in this repository. Reconstructing cohorts requires authorized access to the underlying GA4 and production records, and a fresh run can differ from this dated snapshot.
