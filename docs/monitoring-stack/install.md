# Installing the Stack

## One-time setup

```bash
git clone https://github.com/atharphy/InnerTracker_monitoring.git
cd InnerTracker_monitoring
cp config/paths.conf.example config/paths.conf
```

Then open `config/paths.conf` and fill in the paths for your machine — at
minimum, where your Ph2_ACF checkout's monitoring config lives
(`PH2ACF_MONITORING_CONFIG`). Everything else in the file has a sensible
default; only change what you actually need to.

```bash
bin/bootstrap.sh
```

This one command:

- Downloads and installs Prometheus and Grafana (at the pinned versions in
  `paths.conf`).
- Installs the one community Grafana plugin the dashboards need.
- Writes out every other config file — Prometheus's config, Grafana's
  dashboard/data source setup, and the Ph2_ACF-side monitoring config — all
  filled in from the one `paths.conf` file. You never hand-edit those.

The two custom Grafana panels this project needs (the 3D viewer and the
query-assistant app) are already included in the repo, so there's nothing
extra to build for those.

## Starting it up

```bash
bin/start_all.sh
```

Starts the exporter, Prometheus, and Grafana, in that order. Grafana's own
startup also arms two background watchers automatically — one that keeps
the dashboards in sync with the cabling database, and one that keeps the
Chip Details dashboard in sync with whatever registers are currently
enabled. You don't need to start those yourself.

Open `http://localhost:<port>` (default `3000`) to see Grafana.

## Day to day

```bash
bin/status_all.sh     # what's running right now
bin/stop_all.sh        # stop everything
bin/reset_all.sh        # stop + start, no data lost - use after editing paths.conf
```

`bin/bootstrap.sh` is safe to run again any time — it skips whatever's
already installed, but always re-writes config from the current
`paths.conf`, so run it again after any change to that file.

## Updating later

```bash
# bump PROMETHEUS_VERSION / GRAFANA_VERSION in config/paths.conf, then:
bin/update_stack.sh
```

This re-fetches whichever pinned version changed and restarts everything.
Your stored data (Prometheus's history, Grafana's own database, the
exporter's CSV archive) is never touched by an update.
