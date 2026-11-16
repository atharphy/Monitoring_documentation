# Connecting to a Central Instance

Prometheus doesn't push data anywhere on its own by default - getting data
to a shared, central Prometheus/Grafana needs one of two deliberate setups.
Both are already built into `config/paths.conf`.

## A. Forward everything to a central Prometheus

Your own local Prometheus keeps scraping the exporter as usual, and also
forwards ("remote writes") everything it collects to a central Prometheus
elsewhere. This is the real setup already running between a remote test
stand and the central instance at CERN.

```ini
# config/paths.conf
PROMETHEUS_REMOTE_WRITE_URL=http://<central-host>:<port>/api/v1/write
```

Run `bin/bootstrap.sh` again to apply it. Leave this blank and your
Prometheus just doesn't forward anywhere - it keeps working fine on its own
either way.

For this to work, the *central* Prometheus has to be willing to accept
incoming data like this (started with `--web.enable-remote-write-receiver`).
This stack's own Prometheus is already started that way too, so it's equally
able to be the *receiving* end for someone else's setup, with no extra
configuration needed for that direction.

## B. Let a remote Prometheus scrape your exporter directly instead

No local forwarding - a Prometheus somewhere else just reads from your
exporter the normal way.

```ini
# config/paths.conf
EXPORTER_LISTEN_ADDRESS=0.0.0.0          # not localhost - must accept outside connections
PROMETHEUS_SERVER_URL_EXTRA=http://<remote-prometheus-host>:<port>
```

Run `bin/bootstrap.sh` again, then add your machine as a scrape target in
the remote Prometheus's own config.

## Either way: open the firewall

Whichever direction the data flows, the receiving machine's firewall needs
to allow it in, scoped to the specific other machine's address - never open
it to everyone:

```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="<sender-ip>" port port="<port>" protocol="tcp" accept'
firewall-cmd --reload
```

## Which one should you use?

- **Option A** if you want your own local Prometheus to keep working
  independently, and just also feed everything into a shared one.
- **Option B** if you'd rather not run local forwarding at all, and let the
  central instance pull directly.

Most setups use **A** - it keeps working even if the central instance is
briefly unreachable, since nothing here depends on it to function locally.
