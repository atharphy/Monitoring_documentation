# Quickstart: Ph2_ACF to Grafana

This page walks through the full path end to end, one time, start to
finish: turning monitoring on in Ph2_ACF, running the stack, and finding
your data in Grafana. Each step links to the section with the full detail.

## 1. Turn monitoring on in Ph2_ACF

In your hardware XML, enable the `<Monitoring>` block and the specific
registers you want:

```xml
<MonitoringSettings>
  <Monitoring type="RD53B" enable="1" silentRunning="0">
    <MonitoringSleepTime> 1000 </MonitoringSleepTime>
    <MonitoringElement device="RD53" register="VDDA" enable="1"/>
    ...
  </Monitoring>
</MonitoringSettings>
```

Check `csvMonitoringSettings.conf` to confirm the CSV allowlist and output
path are set the way you want.

Run your calibration or DAQ as normal - Ph2_ACF writes the monitoring CSV
while it runs.

Details: [Ph2_ACF CSV Monitoring](ph2acf-monitoring/index.md),
[What can be monitored](ph2acf-monitoring/what-can-be-monitored.md).

## 2. (Optional) Map chips to real module/portcard names

If you want dashboards to show real module and portcard names instead of
`-1`, make sure `dca_mapping_file = auto` is set in
`csvMonitoringSettings.conf` so Ph2_ACF runs the DCA lookup automatically
before recording.

Details: [DCA Query](dca-query/index.md).

## 3. Install and start the monitoring stack

One time:

```bash
git clone https://github.com/atharphy/InnerTracker_monitoring.git
cd InnerTracker_monitoring
cp config/paths.conf.example config/paths.conf
# edit config/paths.conf: at minimum, set PH2ACF_MONITORING_CONFIG
bin/bootstrap.sh
```

Then, every time you want monitoring running:

```bash
bin/start_all.sh
```

This starts the exporter (reads the CSV), Prometheus (stores the values),
and Grafana (draws the dashboards), plus the background watchers that keep
dashboards in sync automatically.

Details: [Installing the Stack](monitoring-stack/install.md),
[Running it locally](monitoring-stack/local-setup.md) or
[Connecting to a central instance](monitoring-stack/central-setup.md) if
you're sending data to a shared Prometheus/Grafana instead.

## 4. Open Grafana and find your data

```
http://localhost:3000
```

(default port - check `config/paths.conf` if you changed it)

Log in and open the **InnerTracker** folder. You'll see:

- **Parts** / **Interactive** - pick a register from the dropdown, see
  every module colored by its current value.
- **3D** - the same idea, drawn on a 3D model of the detector.
- **Chip Details** - pick a specific module/chip and see every one of its
  registers at once, one panel each.

If a dashboard is empty, check `bin/status_all.sh` first - usually it means
the exporter or Prometheus isn't running, or Ph2_ACF hasn't written any CSV
rows yet.

Details: [Dashboards](dashboards/index.md).

## That's the whole path

```
Ph2_ACF (writes CSV)
   -> exporter (CSV -> Prometheus metrics)
   -> Prometheus (stores history)
   -> Grafana (dashboards)
```

Everything past this point in the documentation goes deeper into each of
these four steps.
