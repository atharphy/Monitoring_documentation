# Dashboards

Once the [monitoring stack](../monitoring-stack/index.md) is running, you
get a folder named **InnerTracker** in Grafana with several dashboards in
it:

| Dashboard | What it shows |
|---|---|
| **Parts** | Register values laid out by physical position (barrel, forward, endcap) - a heatmap-style view, one tile per module position. |
| **Interactive** | Similar to Parts, but with a register picker and more interactive filtering. |
| **3D** | A real 3D model of the detector, with modules, portcards, and cabling - colored by whatever register and limits you pick. |
| **Chip Details** | One panel per register, for a specific module/chip you select from a dropdown - the detailed, per-chip view. |

The rest of this section covers what you can actually see and monitor on
each physical part: [portcards](portcards.md), [lpGBT](lpgbt.md),
[modules](modules.md), and [chips](chips.md).

## How they stay up to date

- **Parts, Interactive, 3D** - all driven by the same shared geometry files
  (which module is where, and which chips belong to it). A background
  watcher re-publishes all three automatically within about 10 seconds of
  any change - you never need to manually regenerate or re-import anything.
- **Chip Details** - rebuilt automatically from whatever registers are
  currently enabled in Prometheus, on its own 10-second timer. Turn on a
  new register in Ph2_ACF (or add a [virtual register](../ph2acf-monitoring/virtual-registers.md))
  and its panel appears here by itself, with nothing to re-run by hand.
