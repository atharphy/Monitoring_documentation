# lpGBT

lpGBT values - temperature, supply voltages, ADC channels, and the rest of
the registers listed in [What Can Be Monitored](../ph2acf-monitoring/what-can-be-monitored.md#real-registers-lpgbt) -
are read by Ph2_ACF, written to the CSV, and exported to Prometheus as
`cmsit_lpgbt_monitor_value` and `cmsit_lpgbt_monitor_last_update_seconds`,
exactly like RD53 values are.

## Current status: no dedicated lpGBT dashboard yet

As of this writing, none of the dashboards build a panel from these two
metrics - the **Chip Details** dashboard only discovers panels from
`cmsit_monitor_value` (the RD53 metric), not the lpGBT one. The data is
there in Prometheus; it just isn't drawn anywhere yet.

If you need an lpGBT value today, you can:

- Query Prometheus directly, e.g.
  `cmsit_lpgbt_monitor_value{register="TEMP"}` at
  `http://<prometheus-host>:<port>/graph`.
- Build a quick panel in Grafana yourself, pointed at the same metric.

## Where this would naturally fit later

The same pattern already used for [Chip Details](chips.md) - discover
whatever registers currently exist in Prometheus, and build one panel per
register automatically - would work the same way for lpGBT values. This is
a known gap, not a limitation of the data itself: the values are already
flowing all the way through the stack, just not drawn yet.
