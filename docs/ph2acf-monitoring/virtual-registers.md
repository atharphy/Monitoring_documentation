# Virtual Registers

Some useful values aren't a single register you can read off the hardware —
they're a combination of two or more. A **virtual register** is a value
calculated from real RD53 and lpGBT readings, defined once, and then
recorded in the CSV (and exported to Prometheus) exactly like a real
register.

## Where they're defined

In a config file, pointed to from `csvMonitoringSettings.conf`:

```ini
virtual_register_config = settings/csvVirtualRegisters.conf
```

Each line in that file defines one virtual register:

```text
NAME | UNIT | SCOPE | EXPRESSION
```

- **NAME** — the register name it will show up as (must be unique), e.g.
  `HEADROOM_A`.
- **UNIT** — the unit to report, e.g. `uA` or `%`. Leave it empty if the
  result has no unit (a ratio, for example).
- **SCOPE** — one of `chip`, `module`, `lpgbt`, or `portcard`. This decides
  which real registers the expression is allowed to use, and how they're
  combined (see below).
- **EXPRESSION** — the actual calculation: real register names combined
  with `+`, `-`, `*`, `/`, parentheses, and unary signs.

## Real examples

```text
HEADROOM_A | | chip | ANA_SHUNT_CURR / ANA_IN_CURR
HEADROOM_D | | chip | DIG_SHUNT_CURR / DIG_IN_CURR
ILOAD_A | uA | chip | ANA_IN_CURR - ANA_SHUNT_CURR
ILOAD_D | uA | chip | DIG_IN_CURR - DIG_SHUNT_CURR

IIN_A | uA | module | sum(ANA_IN_CURR[*])
IIN_D | uA | module | sum(DIG_IN_CURR[*])
IIN | uA | module | sum(ANA_IN_CURR[*]) + sum(DIG_IN_CURR[*])
VIN | V | module | avg(VINA[*], VIND[*])
DIGITAL_CONSUMPTION | % | module | 100 * sum(DIG_IN_CURR[*]) / (sum(ANA_IN_CURR[*]) + sum(DIG_IN_CURR[*]))
ANALOG_CONSUMPTION | % | module | 100 * sum(ANA_IN_CURR[*]) / (sum(ANA_IN_CURR[*]) + sum(DIG_IN_CURR[*]))
```

- `HEADROOM_A`/`HEADROOM_D` — how much of the shunt current is being used,
  as a fraction of the total input current (one value per chip).
- `ILOAD_A`/`ILOAD_D` — the actual load current, after subtracting what's
  going through the shunt (one value per chip).
- `IIN`, `IIN_A`, `IIN_D` — total current drawn by the whole module, adding
  up every chip on it.
- `VIN` — average input voltage across the module.
- `DIGITAL_CONSUMPTION`/`ANALOG_CONSUMPTION` — what percentage of the
  module's total current is digital vs. analog.

## Picking values from more than one chip: scope

- **`chip`** — the expression only uses registers from the one chip it's
  being calculated for, e.g. `ANA_SHUNT_CURR / ANA_IN_CURR`.
- **`module`** — can pull a register from a specific chip by index
  (`VINA[0] + VINA[1]`), or from every chip on the module at once using
  `REGISTER[*]`, which the functions below then combine.
- **`lpgbt`** / **`portcard`** — the same idea, but using lpGBT registers
  instead (written as `LPGBT_<register>`, e.g. `LPGBT_VDD - LPGBT_VDDTX`). A
  `portcard`-scoped expression can also pick a specific lpGBT on the
  portcard by its optical group ID (`LPGBT_VDD[0] - LPGBT_VDD[1]`), or use
  `[*]` across all lpGBTs on that portcard (at most 3).

## Combining values across chips

- `sum(ANA_IN_CURR[*])` — adds up that register across every chip selected.
- `avg(VINA[*], VIND[*])` — averages everything across both lists together.

## A few rules

- If a real register an expression needs is missing (not enabled, or failed
  to read), that virtual register is simply skipped for that cycle — it
  doesn't produce a wrong or zero value.
- After changing this file, restart `CMSITminiDAQ` — virtual registers are
  loaded once at startup, not re-read live.
