# Monitoring Documentation

This TWiki explains how monitoring works for the Inner Tracker, from the
raw data coming out of Ph2_ACF to the dashboards you look at in Grafana.

New here? **[Quickstart: Ph2_ACF to Grafana](quickstart.md)** walks through
the whole path, start to finish, in one page.

The rest of the TWiki covers four things in more depth:

1. **[Ph2_ACF CSV monitoring](ph2acf-monitoring/index.md)** - how Ph2_ACF
   records values while it runs, what it can record, and what "virtual
   registers" are.
2. **[DCA Query](dca-query/index.md)** - the tool that figures out which
   module and portcard each chip belongs to, by looking it up in CERN's
   construction database.
3. **[Monitoring stack](monitoring-stack/index.md)** - how to install and
   run the whole pipeline (exporter, Prometheus, Grafana), locally or
   connected to a central instance.
4. **[Dashboards](dashboards/index.md)** - what you can actually see and
   monitor once it's running: portcards, lpGBT, modules, and chips.

If you only need to *run* the stack (not understand every detail), the repo
itself has a step-by-step README:
<https://github.com/atharphy/InnerTracker_monitoring>. This TWiki explains the
concepts behind it and the pieces that live outside that repo.
