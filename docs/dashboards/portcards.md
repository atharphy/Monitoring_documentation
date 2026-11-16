# Portcards

A portcard carries one or more lpGBTs, and connects a group of modules back
to the DTC. Portcards currently show up visually in the **3D dashboard** -
turn on **Portcards / cartridges** to see them in the model.

## Registers

There's no dedicated "portcard register" dashboard laid out by physical
position yet. For now:

- Individual lpGBT registers (`LPGBT_TEMP`, etc.) and any `portcard`-scoped
  [virtual register](../ph2acf-monitoring/virtual-registers.md) both show up
  in **Chip Details** - see [lpGBT](lpgbt.md) for how that works.
