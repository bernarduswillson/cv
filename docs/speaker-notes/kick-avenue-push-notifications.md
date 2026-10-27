# Kick Avenue - Point 4: Push notifications and re-engagement

**Keynote:** I resolved legacy push bugs causing duplicate sends and missed deliveries by adding atomic replay claims, correcting franchise credentials, and restoring pending-push recovery. I also fixed ignored notification preferences and added review and browsing reminders. In a retrospective production-database analysis, 1,984 of 7,801 users in a browsing-reminder cohort returned to product browsing in the app within 24 hours of a recorded send attempt. The rounded observed return rate is 25% and applies only to browsing reminders.

**Calculation and scope:** 1,984 / 7,801 × 100 = **25.43%**, rounded to **25%**. The October 27, 2026 snapshot covers attempts on October 21-25 in Asia/Jakarta. Match `browsing_reminders.user_id` to `product_variant_view.user_id`, requiring an iOS/Android product view after `handoff_at` and within 24 hours. Count each user once, regardless of the product viewed. Retain attempts with a seven-day cooldown, excluding definite rejections whose cooldown was released.

**If asked:** `handoff_at` records a send attempt, not confirmed delivery or a click. Only the latest attempt is retained, so the analysis uses recent cohorts still within their seven-day cooldown. Returns may be organic or related to other campaigns. This is not a 25% uplift or proof of notification-driven reactivation.

[Aggregate counts, query, limitations, and implementation PRs](../metrics/push-notifications/2026-10-27-browsing-reminder-cohort.json)
