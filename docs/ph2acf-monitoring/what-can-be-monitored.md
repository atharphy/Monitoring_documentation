# What Can Be Monitored

## Real registers: RD53

This is the full list of RD53 registers the `<Monitoring>` block supports,
taken from a real run's hardware XML. A basic setup only turns on the first
group; the rest (grouped below as "extra") are there to turn on if you need
them, but most setups leave them off (`enable="0"`).

**Core - on in a typical setup:**

| Register | What it is |
|---|---|
| `VINA` | Analog input voltage |
| `VDDA` | Analog supply voltage |
| `ANA_IN_CURR` | Analog input current |
| `VIND` | Digital input voltage |
| `VDDD` | Digital supply voltage |
| `DIG_IN_CURR` | Digital input current |
| `Iref` | Reference current |
| `ANA_SHUNT_CURR` | Analog shunt current |
| `DIG_SHUNT_CURR` | Digital shunt current |

**Temperature sensors:**

| Register | What it is |
|---|---|
| `POLY_REL_TEMPSENS_TOP` | Polysilicon temperature sensor, top: relative |
| `POLY_REL_TEMPSENS_BOTTOM` | Polysilicon temperature sensor, bottom: relative |
| `POLY_ABS_TEMPSENS_TOP` | Polysilicon temperature sensor, top: absolute |
| `POLY_ABS_TEMPSENS_BOTTOM` | Polysilicon temperature sensor, bottom: absolute |
| `TEMPSENS_ANA_SLDO` | Temperature sensor on the analog SLDO regulator |
| `TEMPSENS_DIG_SLDO` | Temperature sensor on the digital SLDO regulator |
| `TEMPSENS_CENTER` | Temperature sensor at the center of the chip |
| `INTERNAL_NTC_REL` | Chip's internal NTC reading: relative |
| `INTERNAL_NTC_ABS` | Chip's internal NTC reading: absolute |
| `NTC_VOLT` | NTC voltage, read directly |
| `NTC_CURR` | NTC current, read directly |

**Extra: references, radiation sensors, front-end tuning, diagnostics**

| Register | What it is |
|---|---|
| `VOFS` | Offset voltage |
| `VrefA` | Analog reference voltage |
| `VrefD` | Digital reference voltage |
| `Vref_CORE` | Core reference voltage |
| `Vref_PRE` | Preamp reference voltage |
| `RADSENS_ANA_SLDO` | Radiation sensor on the analog SLDO regulator |
| `RADSENS_DIG_SLDO` | Radiation sensor on the digital SLDO regulator |
| `RADSENS_CENTER` | Radiation sensor at the center of the chip |
| `VCAL_HI` | High calibration voltage |
| `VCAL_MED` | Medium calibration voltage |
| `LIN_FE_REF_KRUMCURR` | Linear front-end reference Krummenacher current |
| `LIN_FE_GDAC_MAIN` / `LIN_FE_GDAC_LEFT` / `LIN_FE_GDAC_RIGHT` | Linear front-end global DAC, by region |
| `LIN_FE_PREAMP_MAIN` / `LIN_FE_PREAMP_LEFT` / `LIN_FE_PREAMP_RIGHT` | Linear front-end preamp bias, by region |
| `LIN_FE_PREAMP_TOP` / `LIN_FE_PREAMP_TOP_LEFT` / `LIN_FE_PREAMP_TOP_RIGHT` | Linear front-end preamp bias, top regions |
| `ANA_GND_0` … `ANA_GND_11` | Analog ground reference, 12 points across the chip |
| `HIGH_Z` | High-impedance diagnostic reading |

## Real registers: lpGBT

| Register | What it is |
|---|---|
| `TEMP` | lpGBT temperature |
| `VDDTX` | Transmitter supply voltage |
| `VDDRX` | Receiver supply voltage |
| `VDDA` | Analog supply voltage |
| `VDD` | Core supply voltage |
| `PUSMStatus` | Power-up state machine status |
| `ADC0`–`ADC7` | Raw ADC channel readings, where used instead of (or in addition to) the named registers above |
| `DLDPFecCorrectionCount0`–`3` | Forward error correction count, one per downlink data path |

Not every lpGBT register is used on every setup - which ones are actually
read depends on what the portcard wiring connects to each ADC channel, so
the exact list can vary between hardware revisions.

## Board-level registers

A small number of values come from the readout board itself rather than from
a chip, using `device="Board"` instead of `RD53`/`LpGBT`. For example:

```xml
<MonitoringElement device="Board" register="user.stat_regs.lpgbt_monitoring.fec_cntr" enable="0"/>
```

This one counts FEC (forward error correction) corrections at the board
level, as a firmware status register rather than a chip register - the
register name is the firmware's own internal path, not a short label like
the chip registers above.

## Turning a register on or off

Each register in the hardware XML has its own `enable="1"` or `enable="0"`.
Only registers set to `1` are actually read off the hardware. To add a
register to what's being recorded, find its line in the `<Monitoring>` block
and change `enable="0"` to `enable="1"` (or vice versa to stop recording it).

## Narrowing it down further: the CSV allowlist

Even after a register is enabled and being read, `csvMonitoringSettings.conf`
has one more filter before it reaches the CSV:

```ini
csv_register_allowlist = *
```

`*` means "write every enabled register to the CSV" - this is the normal
setting. You can restrict it to specific register names (comma-separated) if
you only want a subset written out, without having to disable them in the
hardware XML.

```ini
csv_include_errors = true
```

Controls whether read errors (a register that failed to read) are written to
the CSV at all, instead of being silently skipped.

## lpGBT values specifically

```ini
lpgbt_csv_enabled = true
lpgbt_csv_register_allowlist = *
```

lpGBT has its own on/off switch and its own allowlist, separate from the RD53
one above - so you can, for example, record every RD53 register but only a
specific lpGBT one, or turn lpGBT recording off entirely while keeping RD53
monitoring on.

## What ends up in Prometheus

Once a register makes it into the CSV, the [exporter](../monitoring-stack/index.md)
turns it into one of these Prometheus metrics:

| Metric | What it means |
|---|---|
| `cmsit_monitor_value` | The actual RD53 register reading |
| `cmsit_monitor_error` | Whether that reading came back as an error |
| `cmsit_monitor_last_update_seconds` | How long ago this value was last updated |
| `cmsit_lpgbt_monitor_value` | The actual lpGBT register reading |
| `cmsit_lpgbt_monitor_last_update_seconds` | How long ago this lpGBT value was last updated |

Every one of these carries labels like `module`, `chip`, `efuse`, and
`register`, which is how Grafana knows which panel a value belongs to. See
[Dashboards](../dashboards/index.md) for how these show up visually.
