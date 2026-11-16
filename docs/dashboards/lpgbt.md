# lpGBT

lpGBT values - temperature, supply voltages, ADC channels, and the rest of
the registers listed in [What Can Be Monitored](../ph2acf-monitoring/what-can-be-monitored.md#real-registers-lpgbt) -
are read by Ph2_ACF, written to the CSV, and exported to Prometheus.

## Where they actually show up

The CSV Ph2_ACF writes today is a single "unified" file that has both RD53
and lpGBT columns in it. Any lpGBT column (named `LPGBT_<register>`, e.g.
`LPGBT_TEMP_value`) is exported as a normal `cmsit_monitor_value` sample -
the **same metric the RD53 registers use** - with `register="LPGBT_TEMP"`
and `portcard_id`/`lpgbt_efuse` labels filled in (`module`/`chip`/`efuse`
are `-1` for a portcard-only row, since an lpGBT reading isn't tied to a
specific chip).

There's a second metric, `cmsit_lpgbt_monitor_value`, but it's fed by a
different, older, standalone lpGBT-only CSV format that isn't what the
unified CSV produces - in the normal setup it stays empty.

## Seeing them in a dashboard

Because lpGBT registers land in `cmsit_monitor_value`, the **Chip Details**
dashboard already picks them up automatically, the same way it picks up any
RD53 register: it's built from whatever `register` values currently exist
on that metric, with no fixed list. Turn an `LPGBT_*` register on and its
panel appears in Chip Details within about 10 seconds, right alongside the
RD53 ones - you just won't see a module/chip selected for it, since those
labels are `-1` on an lpGBT row.

Parts, Interactive, and the 3D dashboard are module-tile views, keyed on
`module`/`chip` - since lpGBT rows don't carry those, lpGBT values don't
appear as a module tile there. If you want a view organized by portcard
instead of by chip, see the note on [Portcards](portcards.md#whats-not-there-yet).

## Querying directly

You can always query a specific lpGBT register in Prometheus without
waiting for a dashboard panel:

```
cmsit_monitor_value{register="LPGBT_TEMP"}
```

at `http://<prometheus-host>:<port>/graph`.
