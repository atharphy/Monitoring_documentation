# Portcards

A portcard carries one or more lpGBTs, and connects a group of modules back
to the DTC. Right now, portcards are mainly a **3D dashboard** feature, not
a Parts/Interactive one.

## Where to see them

In the 3D dashboard, turn on:

- **Portcards / cartridges** — shows the physical portcards in the model.
- **Connected portcards only** — shows only the portcards that actually
  have a real connection defined (board ID, and a module/lpGBT link), from
  `hardware_geometry/portcards.yaml`. Everything else is hidden, instead of
  showing placeholder/unconnected positions.
- **Service antlers** — the cabling structures that branch out across
  modules on a portcard.

The cabling shown is for visual routing — it illustrates roughly how
portcards and modules are connected, but it's not itself what decides
whether something counts as "connected"; that comes from the actual mapping
data.

## What's not there yet

There's currently no dedicated "portcard register" dashboard — lpGBT
register values (see [lpGBT](lpgbt.md)) are exported to Prometheus, but no
dashboard panel reads them yet. If you need to see an actual lpGBT reading
today, you'd query Prometheus directly for `cmsit_lpgbt_monitor_value`
rather than finding it in a dashboard.
