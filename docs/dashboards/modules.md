# Modules

Modules are where most of the dashboards focus - this is the level the
**Parts**, **Interactive**, and **3D** dashboards are all built around.

## Parts and Interactive

Each of these shows one register at a time, laid out by physical position -
barrel (TBPX), forward (TFPX), or endcap (TEPX) - with one tile per module.
You pick the register from a dropdown; the dashboard colors each module
tile by its current value, against the high/low limits you set at the top
of the page.

Interactive adds more filtering on top of the same idea - it's built for
digging into a specific subset of modules rather than scanning the whole
detector at once.

## 3D

The 3D dashboard shows modules as real objects in a 3D model of the
detector, not as flat tiles. A few relevant toggles:

- **Modules / Chips** appearance - switch between coloring whole modules, or
  the individual chips on each module.
- **Installed modules only** - show only the modules that are actually
  defined in the shared geometry (the ones you, or the cabling database
  sync, have added), hiding every unfilled position instead of showing an
  empty/placeholder module there.
- **Installed close-up** - zooms in and hides the surrounding structure, for
  a clean view of just the installed modules.

## What decides which modules show up

The Parts/Interactive/3D dashboards are all driven by the same shared
geometry files (`barrel_geometry.yaml`, `forward_geometry.yaml`,
`endcap_geometry.yaml` in the monitoring stack repo) - a module shows up
once it has an entry there, either added automatically from the cabling
database, or by hand for a module not in the database yet. See the
[monitoring stack](../monitoring-stack/index.md) section for how that sync
works.

## Where to find per-chip detail

To look at a specific module's chips in depth - every register, one panel
each - see [Chips](chips.md).
