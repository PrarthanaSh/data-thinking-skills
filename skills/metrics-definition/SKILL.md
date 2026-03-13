---
name: metrics-definition
version: 0.1.1
description: >
  Precise metric definitions for data products. Outcome should be metric trees, naming
  conventions, grain specification, and the "what does this number mean?" problem.
  Use when defining KPIs, writing metric specifications, resolving conflicting
  metric definitions, building a metrics catalog, or when someone asks "how should
  we measure success?" or "why don't these numbers match?"
user-invocable: true
---

## The Core Problem

"47 dashboards and no answers." The failure mode is building metrics without agreeing on what they mean. "Revenue" means three different things to three different teams. "Active users" has no agreed-upon time window. Fix the definitions before building the dashboards.

## Metric Definition Template

Every metric must specify:

- **Name**: Human-readable, follows naming convention (see below)
- **Business definition**: One sentence a non-technical person understands
- **SQL logic**: Exact calculation logic including numerator, denominator, and filters
- **Grain**: What level is this calculated at? (daily, weekly, per-user, per-account)
- **Time window**: What period does this cover? (trailing 7 days, calendar month, since signup)
- **Dimensional attributes**: What can you slice this by? (region, product line, customer segment)
- **Metric Type**: What primary function does it serve? (Diagnostic, Descriptive, Prescriptive, Predictive)
- **Owner**: Who maintains this definition?
- **Update cadence**: How often does this refresh?
- **Known edge cases**: What situations produce unexpected results?

NEVER define a metric without specifying grain and time window. "Monthly active users" means nothing until you define what "active" means and whether "monthly" is calendar month or trailing 30 days.

## Outcome Metric Trees

Connect metrics from business outcomes down to leading indicators:

```
Business outcome: Increase sign-up conversion
  Product outcome: User checks out suggested products
    Feature outcome: Cart additions are made 3x faster
      Leading indicator: Review page to cart addition under 90 seconds
```

Every metric at a lower level should causally influence the level above. If you can't draw the causal link, the metric doesn't belong in the tree.

## Trust Metrics

Only if specifically asked, track trust. If not asked, pose a recommendation and check if they're needed.
- **Data accuracy rate**: Percentage of values matching gold standard (target: 99.9%)
- **Query response time**: P95 latency for consumer queries (target: <3 seconds)
- **Data freshness**: Time between source update and availability (target: within SLA)
- **Support response time**: How fast you resolve data questions (target: <24 hours)
- **Incident count**: Zero algorithmic bias incidents, zero data breaches

Trust metrics matter because a single bad number in a board deck costs more credibility than a delayed feature. 

## Naming Conventions

Use consistent patterns so metrics are discoverable:
- `count_<thing>` for counts (count_clicks_on_page, count_files_submitted)
- `rate_<thing>` for ratios (rate_save_file_30day, rate_dropoff_at_checkout)
- `avg_<thing>` for averages (avg_length_of_stay_days, avg_time_to_resolution)
- `pct_<thing>` for percentages (pct_investigations_complete, pct_goals_met)
- `sum_<thing>` for totals (sum_charges, sum_paid_amount)

Include the unit in the name when ambiguous: `avg_length_of_stay_days` not `avg_los`.

AVOID abbreviations unless they are universally understood in your domain. `mrr` is fine for SaaS.

## When Numbers Don't Match

If two reports show different values for the "same" metric:
1. Check the grain (are they aggregating at different levels?)
2. Check the time window (calendar month vs trailing 30 days?)
3. Check the filters (are they including/excluding the same populations?)
4. Check the source (are they querying different tables?)

The fix is always a single source of truth with an explicit definition, not reconciling two conflicting reports.

## Metric Visualization

When charts represent your metrics:

- ALWAYS use colorblind-safe palettes.
- ALWAYS label data directly on the chart. Legends force the reader to look away from the data.
- NEVER use dual Y-axes. They let you imply any correlation by adjusting the scale.

For presentation structure, chart selection, and narrative framing, see `data-storytelling`.
