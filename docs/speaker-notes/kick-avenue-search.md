# Kick Avenue - Point 2: Search and product discovery

**Keynote:** I maintained and extended the existing OpenSearch-powered search across backend, web, and mobile. Volume is estimated at approximately 350,000 daily search requests using sampled search shares and Grafana traffic. The 127 ms median API response time was measured directly in the samples.

**Calculation and scope:** Four October 27, 2026 samples from all three ingress pods captured **11,927 requests in 37 minutes**, limited to `GET /search` and `GET /prod/search` reaching the Octane API. Search represented **8.5-10.4%** of the simultaneous Caddy all-endpoint counts. Linearly interpolate these shares between sample midpoints, including overnight, then weight them by Grafana's 15-minute traffic profiles and daily totals for **September 27-October 26**. The model averages **350,393.64 search requests/day**, rounded to **350,000**. It assumes the observed shares represent other days and unsampled hours. Pooling **11,873 HTTP 200** ingress durations gives the separately measured **127 ms median**. Cache hits and misses are combined. Daily volume is modeled, while latency covers the sampled windows.

[Daily estimate and assumptions](../metrics/search-sampling/2026-10-27-daily-search-estimate.json) · [Observed sample windows](../metrics/search-sampling/2026-10-27-summary.json)
