# Monitoring Documentation

Source for the Inner Tracker monitoring documentation site, built with
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Building locally

```bash
python3 -m pip install -r requirements.txt
mkdocs serve
```

Then open `http://127.0.0.1:8000`.

## Structure

See `mkdocs.yml` for the page hierarchy. The pages live under `docs/`,
grouped into four sections: Ph2_ACF CSV monitoring, DCA Query, the
monitoring stack, and the dashboards.
