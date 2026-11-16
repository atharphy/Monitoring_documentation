# Portcards

A portcard carries one or more lpGBTs, and connects a group of modules back
to the DTC. Right now, portcards are mainly a **3D dashboard** feature, not
a Parts/Interactive one.

## Where to see them

In the 3D dashboard, turn on:

- **Portcards / cartridges** - shows the physical portcards in the model.
- **Connected portcards only** - shows only the portcards that actually
  have a real connection defined (board ID, and a module/lpGBT link), from
  `hardware_geometry/portcards.yaml`. Everything else is hidden, instead of
  showing placeholder/unconnected positions.
- **Service antlers** - the cabling structures that branch out across
  modules on a portcard.

The cabling shown is for visual routing - it illustrates roughly how
portcards and modules are connected, but it's not itself what decides
whether something counts as "connected"; that comes from the actual mapping
data.

## What's not there yet

There's currently no dedicated "portcard register" dashboard laid out by
physical position the way Parts/Interactive are for modules. lpGBT register
values (see [lpGBT](lpgbt.md)) do show up today, though - they land in
`cmsit_monitor_value` alongside the RD53 ones, so the **Chip Details**
dashboard already has a panel for any lpGBT register you've enabled
(`register="LPGBT_TEMP"` and so on); there just isn't a tile-per-portcard
view of them yet. If you need one directly, query Prometheus for
`cmsit_monitor_value{register="LPGBT_TEMP"}` (or whichever register you
want).
