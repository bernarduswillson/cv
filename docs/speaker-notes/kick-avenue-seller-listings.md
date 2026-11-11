# Kick Avenue: Seller listing performance

**Keynote:** I optimized Laravel seller listing queries by batching highest-bid loading, pushing seller and group predicates into SQL, paginating IDs before loading details, and using bounded SQL aggregates. This removed per-listing queries and unnecessary hydration from expensive seller-list paths.

**Evidence:** [PR #675](https://github.com/kickavenue/be-api-laravel/pull/675), merged August 4, 2026. Its historical benchmark for a seller returning approximately 218,000 results records a 16-row cold request improving from **17.27 s to 3.07 s** (31 to 20 SQL queries). The CV rounds these to **17.3 s and 3.1 s**. A 100-row request improved from 24.19 s to 3.08 s (115 to 20 queries); an independent seller scenario improved from 15.95 s to 2.91 s.

**Measurement limits:** These were development-client requests against a read-only production database, including network latency. They are historical PR measurements, not measurements rerun during this CV review, a production p95, or a site-wide average. The extreme active-status path remained approximately 11.54 s. Response parity checks were documented in the PR; the original profiler artifacts were not retrieved.

**Deployment:** After repository history rewrites, the corresponding rebased main commit was verified as an ancestor of the successful [November 11 production deployment](https://github.com/kickavenue/be-api-laravel/actions/runs/43001319435). Deployment confirms the code reached production; it does not establish a separate production latency or business uplift measurement.

**Follow-up research:** A [July 30 Jira comment](https://bintang5.atlassian.net/browse/KB-5375?focusedCommentId=48995) documents a separate cold-request pair of 17.0848 s to 2.520 s for 218,729 listings. Its sample count and exact code revisions are unspecified, so the CV retains the more fully documented PR comparison. Do not combine baselines and results from different runs.

[PR #678](https://github.com/kickavenue/be-api-laravel/pull/678) reports 474.65 ms to 6.10 ms p95 under cache invalidations, but concerns public product-detail listings rather than the authenticated seller-list endpoint and was closed unmerged. Its cold rebuild remained 475.19 ms. [Open PR #1157](https://github.com/kickavenue/be-api-laravel/pull/1157) contains a later seller-list role-lookup optimization but no measured timing or deployment evidence. Neither supports replacing the CV's 3.1 s result.
