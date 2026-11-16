# Monitoring Stack

**Repo:** <https://github.com/atharphy/InnerTracker_monitoring>

This is the pipeline that turns the CSV files from
[Ph2_ACF CSV monitoring](../ph2acf-monitoring/index.md) into the dashboards
you actually look at.

## The pieces, in order

```
Ph2_ACF  -->  CSV exporter  -->  Prometheus  -->  Grafana
 (writes       (reads the        (stores the       (shows it as
  CSV files)    CSV, serves       values over        dashboards)
                it as metrics)    time)
```

1. **Ph2_ACF** writes the monitoring CSV, as covered in the previous
   section.
2. **The CSV exporter** (`components/exporter` in the repo) tails that CSV
   and turns each row into a Prometheus metric — `cmsit_monitor_value`,
   `cmsit_lpgbt_monitor_value`, and a few others.
3. **Prometheus** scrapes the exporter every second and stores the values,
   so you can look back at recent history, not just the latest value.
4. **Grafana** reads from Prometheus and draws the dashboards — the parts
   view, the interactive view, the 3D view, and Chip Details.

Every piece in this chain has its own start/stop script in the repo, plus a
single `bin/start_all.sh` / `bin/stop_all.sh` that drives all of them
together, and one file, `config/paths.conf`, where every path and setting
lives.

## What's in this section

- **[Installing the stack](install.md)** — the actual step-by-step commands.
- **[Running it locally](local-setup.md)** — your own Prometheus + Grafana,
  nothing shared with anyone else.
- **[Connecting to a central instance](central-setup.md)** — sending your
  data to (or receiving from) a shared Prometheus/Grafana instead.

This documentation explains the ideas; the repo's own `README.md` is the
up-to-date, authoritative source for exact commands — if the two ever
disagree, trust the repo.
