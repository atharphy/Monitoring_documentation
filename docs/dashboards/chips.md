# Chips

The **Chip Details** dashboard is the detailed, per-chip view - instead of
one tile per module like Parts/Interactive, you pick a module, an eFuse, and
a chip from dropdowns, and see every register for that one chip at once,
one panel each.

## What decides which panels exist

Chip Details doesn't have a fixed list of panels - it's built automatically
from whatever `register` values currently exist in Prometheus (on the
`cmsit_monitor_value` metric). Turn on a new RD53 register in Ph2_ACF (see
[What Can Be Monitored](../ph2acf-monitoring/what-can-be-monitored.md)), or
add a new [virtual register](../ph2acf-monitoring/virtual-registers.md),
and its panel appears here within about 10 seconds - nothing to regenerate
or re-import by hand. The same applies in reverse: stop recording a register
and its panel disappears.

This only covers RD53 registers today - see [lpGBT](lpgbt.md) for the
current status of lpGBT values, which aren't shown here yet.

## Freshness

Each panel marks a value as stale if it hasn't been updated in a while
(normally 120 seconds), so you can tell a frozen/disconnected chip apart
from one that's just reporting a steady value.

## How this fits with the other dashboards

The **3D** dashboard's module panel is configured to link out to Chip
Details - clicking a module there takes you here. From Parts and
Interactive, you currently pick the module/eFuse/chip yourself from the
dropdowns at the top of the page.
