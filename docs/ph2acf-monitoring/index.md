# Ph2_ACF CSV Monitoring

Ph2_ACF can record live values from the hardware - temperatures, voltages,
currents, and more - while it runs, and write them out as CSV files. This is
the starting point for everything else in this documentation: the
[exporter](../monitoring-stack/index.md) reads these CSVs and turns them
into Prometheus metrics, which Grafana then shows as dashboards.

## Turning it on

Monitoring is controlled in two places:

1. **The hardware XML** - the `<Monitoring>` block decides *which* registers
   are read off the hardware at all. Each register is listed with
   `enable="1"` or `enable="0"`:

   ```xml
   <MonitoringSettings>
     <Monitoring type="RD53B" enable="1" silentRunning="0" csv="1">
       <MonitoringSleepTime> 1000 </MonitoringSleepTime>
       <MonitoringElement device="RD53"  register="VDDA" enable="1"/>
       <MonitoringElement device="RD53"  register="VDDD" enable="1"/>
       <MonitoringElement device="LpGBT" register="TEMP" enable="0"/>
       ...
     </Monitoring>
   </MonitoringSettings>
   ```

   `enable="1"` on the whole `<Monitoring>` block turns monitoring on for the
   run at all. `csv="1"` is what actually makes it write a CSV file - without
   it, monitoring can still run (and still respects
   [monitoring windows](monitoring-windows.md)) but nothing is recorded to
   disk. `MonitoringSleepTime` (in milliseconds) is how often it reads the
   registers.

2. **`csvMonitoringSettings.conf`** - a separate config file that controls
   *how* the CSV is written: where it goes, how it rotates, which of the
   enabled registers actually make it into the CSV, and the settings the
   [exporter](../monitoring-stack/index.md) needs to find and read those
   files. See the full list of options in
   [What can be monitored](what-can-be-monitored.md).

See [What can be monitored](what-can-be-monitored.md) for the real list of
RD53 and lpGBT registers, [Monitoring windows](monitoring-windows.md) for
*when* values get recorded during a calibration, and
[Virtual registers](virtual-registers.md) for values that aren't a single
hardware register but are still worth recording.
