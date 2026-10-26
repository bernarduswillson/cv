# Kick Avenue - Point 1: Customer reviews

**Keynote:** I owned customer reviews, ratings, and photo uploads across the backend, web, and mobile apps. In a retrospective analysis, I matched identifiable mobile review-screen viewers to production payment records. Of 3,476 accounts with no prior recorded paid purchase, 468 made their first paid purchase within seven days of their first recorded review view in the analysis period. That is a 13.5% conversion rate. It describes the purchase journey we observed, rather than a proven increase caused by reviews.

**Calculation:** 468 / 3,476 × 100 = 13.4638%, rounded to **13.5%**. Each account is counted once.

**Scope:** Review views from August 27 to October 18, 2026, with payments observed through October 25. GA4 mobile screens were matched through `customUser:id` to production paid sales. Staff, existing buyers, and cancelled/refunded/returned outcomes were excluded. Follow-up was seven days from the index review view, chosen for this analysis. Dates are in Asia/Jakarta and do not establish the release date.

**If asked:** This is an account-level first-purchase rate with no non-viewer comparison group. It does not prove that the purchased product was the one reviewed. The aggregate counts come from the earlier analysis, not a fresh extraction.
