# DCA Query

**Repo:** <https://gitlab.cern.ch/cms_tk_ph2/dcaquery4it>

## What it's for

Every chip and lpGBT has a unique ID burned into it, called an **eFuse**.
That ID alone doesn't tell you *which module* or *which portcard* the chip
is sitting on — for that, you need to look it up in CERN's part database.

**DCA** stands for **Detector Construction Application** — it's the CERN
database that records which chip went into which module, and which module
went into which portcard, during construction. This tool queries that
database automatically.

Without this step, the monitoring dashboards would show `-1` instead of a
real module or portcard name.

## What it does, in one command

```bash
./DCAquery4IT -f CMSIT.xml
```

This reads the eFuse values directly from the hardware through Ph2_ACF,
looks each one up in the DCA database, and writes a CSV file
(`CMSIT.csv`, next to the XML) that maps every chip and lpGBT to its real
module ID and portcard ID.

A full log of what happened is saved automatically as
`CMSIT.xml.DCAquery4IT.log`.

## Before you can run it

- You need a built Ph2_ACF checkout.
- `dcaquery4it` expects to sit right next to it:

```
some_folder/
  Ph2_ACF/
  dcaquery4it/
```

  If your layout is different, set the environment variable
  `PH2ACF_BASE_DIR` to point at your Ph2_ACF checkout.

- You need network access to `https://cmsdca.cern.ch/trk_rhapi` (CERN's DCA
  server). If you don't have it, see [Working offline](#working-offline)
  below.

## Setting it up

From inside the `dcaquery4it` folder:

```bash
source setup_dcaquery4it.sh
```

This adds `DCAquery4IT` to your `PATH`, so you can call it from anywhere —
the same way you'd call `CMSITminiDAQ`. If your Ph2_ACF checkout is also
right next to this one, this also sets `PH2ACF_BASE_DIR` for you
automatically.

The first time you run it, a few Python packages it needs
(`requests`, `beautifulsoup4`, `ilock`) are installed automatically. If you'd
rather install them yourself ahead of time:

```bash
./install_dependencies.sh
```

## Checking the connection first

Before running a real lookup, you can just check whether the DCA server is
reachable from your machine:

```bash
./check_dca.py
```

This prints a clear yes/no — useful if a lookup seems to be hanging or
failing, to rule out a network problem first.

## All the options

```text
-f, --file FILE             Ph2_ACF hardware XML file (required)
-o, --output FILE           Output CSV (default: same name as the XML, with .csv)
-l, --logs                  Show the full hardware and DCA logs on screen
    --dummy-module-id ID    Use ID when a module isn't found in DCA
    --dummy-portcard-id ID  Use ID when a portcard isn't found in DCA
    --no-dca                Skip DCA entirely, use empty/dummy identifiers
-h, --help                  Show the usage message
```

## Working offline

If you don't have network access to CERN's DCA server, or a particular
chip/lpGBT genuinely isn't in DCA yet, you have two options:

- `--no-dca` — skip the lookup completely. Every chip gets a dummy module and
  portcard ID instead of a real one.
- `--dummy-module-id` / `--dummy-portcard-id` — still try DCA, but fall back
  to a fixed value for anything that isn't found, instead of asking you
  interactively.

## How this connects to the monitoring stack

Ph2_ACF's monitoring config (`csvMonitoringSettings.conf`) has a setting
called `dca_mapping_file`. When it's set to `auto`, Ph2_ACF runs this lookup
automatically before it starts recording data, so the monitoring CSV — and
everything built from it, including the dashboards — already has the right
module and portcard names from the start. You only need to run `DCAquery4IT`
by hand when you want a fresh mapping outside of that automatic flow (for
example, to check a specific hardware XML on its own).

## Under the hood

- `DCAquery4IT` runs two Ph2_ACF scripts, `getChipInfo.py` and
  `getLpGBTInfo.py`, to read the eFuses off the hardware, then hands the
  results to `lookup.py`.
- `lookup.py` merges the chip and lpGBT readings, queries DCA, and writes the
  final CSV.
- `rhapi.py` is the actual client that talks to DCA's `RHAPI` web service. It
  comes from a separate shared repo,
  [resthub-dev](https://gitlab.cern.ch/cms-ph2-database/resthub-dev), so if
  the DCA query logic itself ever needs fixing, that's the repo to look at.
