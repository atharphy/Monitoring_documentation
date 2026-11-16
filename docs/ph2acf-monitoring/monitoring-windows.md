# Monitoring Windows

By default, Ph2_ACF records a value every time it reads the hardware
(controlled by `MonitoringSleepTime` in the XML), for the whole duration of
a run. For some calibrations that's more data than you actually want -
monitoring windows let you record only during specific parts of a scan.

## The two modes

In `csvMonitoringSettings.conf`:

```ini
monitor_schedule = always
monitor_percent_windows = 0-10,20-30,40-60,70-80,90-100
```

- **`always`** - record continuously, for the whole run. This is the
  default.
- **`percent`** - only record while the scan's own progress is inside one
  of the listed windows.

`monitor_percent_windows` is a comma-separated list of `start-end` ranges,
in percent of the scan's progress (0 to 100). A value only gets recorded if
the scan's current progress falls inside one of these ranges. The example
above records during the first 10%, then 20–30%, then 40–60%, and so on -
skipping everything in between.

Progress here means the scan's own native completion percentage (how far
through its steps the calibration actually is), not elapsed time.

This check happens before the register is even read off the hardware, not
just before it's written to a CSV file. So the window still applies even
with CSV output turned off (`csv="0"` on the `<Monitoring>` block, or
`enabled = false` in `csvMonitoringSettings.conf`) - it also controls
whether a value is read and sent to things like the live DQM monitor, not
only what ends up in the CSV.

## Setting this per calibration

The settings above are the default, used by any calibration that doesn't
have its own override. To set a different schedule for one specific
calibration, add two lines for it:

```ini
calibration.pixelalive.monitor_schedule = percent
calibration.pixelalive.monitor_percent_windows = 0-10,20-30,40-60,70-80,90-100
```

Once a calibration has its own `monitor_schedule`, it uses **only** its own
windows - it ignores the default lines above completely, even if the
calibration's own `monitor_percent_windows` line is missing (in which case
it would be an error, since `percent` mode requires at least one window).

Calibrations that run without a natural "progress" value - things that just
run until they're told to stop, like `physics` and `realtimemonitor` - only
make sense with `monitor_schedule = always`, which is why they're normally
left that way:

```ini
calibration.physics.monitor_schedule = always
calibration.realtimemonitor.monitor_schedule = always
```

## Why use windows at all

- Smaller CSV files, when you don't need every single sample.
- Still captures the interesting regions of a calibration (start, middle,
  end) without recording the parts you don't care about.
- Keeps Prometheus and the dashboards focused on meaningful snapshots
  instead of a flood of near-identical values from a scan that runs for a
  long time.
