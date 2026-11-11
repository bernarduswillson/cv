# Kick Avenue: CI/CD, payments, and reporting

## CI/CD safeguards

**Keynote:** Added isolated Kubernetes migration jobs before staging and production rollout, with deadlines, failure diagnostics, and rollout blocking on migration failures. Added an 80% coverage requirement for changed executable application lines.

**Evidence:** [Migration gate #973](https://github.com/kickavenue/be-api-laravel/pull/973) and [changed-line coverage #974](https://github.com/kickavenue/be-api-laravel/pull/974), both merged September 17, 2026. The November 11 production workflow passed migration and deployment steps.

**Limits:** 80% is a configured changed-line coverage threshold, not whole-repository coverage. No incident-reduction or deployment-speed improvement was measured.

## DANA integration

**Keynote:** Integrated DANA through Midtrans in Laravel payment flows and Flutter checkout and offers. Backend work covers callbacks, transaction verification, payment lifecycle handling, and reconciliation of ambiguous attempts. Mobile work handles external wallet redirects and checks payment outcomes through the API when callbacks omit a status code.

**Evidence:** [Laravel #1013](https://github.com/kickavenue/be-api-laravel/pull/1013) and [Flutter #432](https://github.com/kickavenue/fe-marketplace-flutter/pull/432), both merged.

**Limits:** No adoption, conversion, or revenue uplift was established. Flutter PR evidence includes sandbox verification; merge status alone does not establish a mobile production release date.

## Next.js payment recovery

**Keynote:** Separated payment creation from gateway handoff, guarded payment initiation, and added authenticated invoice/status recovery so interrupted top-ups can resume without creating another payment request.

**Evidence:** [Next.js #575](https://github.com/kickavenue/fe-marketplace-next/pull/575) and [#584](https://github.com/kickavenue/fe-marketplace-next/pull/584), both merged into staging.

**Limits:** These establish implemented recovery behavior. No measured production payment-success uplift or reduction in duplicate transactions was established.

## Consignment reporting

**Keynote:** Replaced in-memory workbook construction with streamed OpenSpout XLSX output and lazy row reads. Published completed workbooks to private storage and emailed expiring signed links. Added safeguards that route unsent RabbitMQ redeliveries to failure recovery instead of repeating expensive export work.

**Evidence:** [Streaming #995](https://github.com/kickavenue/be-api-laravel/pull/995), [private links #996](https://github.com/kickavenue/be-api-laravel/pull/996), and [redelivery safeguards #999](https://github.com/kickavenue/be-api-laravel/pull/999), merged September 28, 2026.

**Limits:** No specific peak-memory or processing-time reduction was measured. Final code reads 200-row chunks; the PR body is stale on chunk size. Redelivery containment is not a guarantee of exactly-once delivery or automatic successful recovery.

## CI runner cleanup

**Keynote:** Removed 380 abandoned Docker volumes to recover 81 GiB on shared CI runners. Added isolated run/attempt volume names, teardown cleanup, and scheduled cleanup of old unused CI volumes, with host and concurrency guards.

**Evidence:** [PR #1077](https://github.com/kickavenue/be-api-laravel/pull/1077), merged October 23, 2026. The recovery result is documented in the PR; the cleanup implementation and production ancestry were verified during the CV review.

**Limits:** 81 GiB describes one historical cleanup, not recurring savings or an independently rerun measurement. No CI failure-rate improvement was measured.
