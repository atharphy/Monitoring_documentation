# Running It Locally

The simplest setup: the exporter, Prometheus, and Grafana all run on the
same machine as Ph2_ACF, and nothing is shared with anyone else. This is
what you get out of the box after [installing the stack](install.md) —
there's nothing extra to turn on.

## If Ph2_ACF runs on a different machine from the stack

This happens, for example, if the cabling database lives on a separate VM
from where you run Grafana.

Point `config/paths.conf`'s `CABLING_DB_HOST` at an SSH alias (set up in
your own `~/.ssh/config`, passwordless) for whichever machine hosts the
cabling database, and set `CABLING_DB_EXPORT_DIR` to the matching path on
*that* machine. Leave `CABLING_DB_HOST` empty if the cabling database is on
the same machine as the stack — it picks the right mode automatically
either way.

If the Ph2_ACF exporter itself runs on a different machine from Prometheus,
add that machine's address as another scrape target in Prometheus's config,
and add this stack's own Prometheus to that other machine's
`prometheus_server_url` setting (comma-separated, if it already lists
others).

## What you get

- Everything stays on your own machine — nothing is sent anywhere else.
- You can still look at history (not just the latest value), because
  Prometheus stores it locally.
- If you later want to also feed a shared, central instance, see
  [Connecting to a central instance](central-setup.md) — it's an addition,
  not a replacement; your local setup keeps working exactly as before.
