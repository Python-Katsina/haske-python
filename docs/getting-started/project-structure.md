# Project Structure

The `haske new` command scaffolds a ready-to-run application. Inspecting the generator reveals how Haske expects your files to be organised and which optional pieces you can safely remove or extend.

```
my-project/
├── app.py              # Application entrypoint created by the scaffold
├── models.py           # Sample SQLAlchemy models
├── requirements.txt    # Runtime dependencies (Haske + Uvicorn)
├── .env                # Environment defaults (debug flag, database URL)
├── static/             # Optional static assets when templates are enabled
└── templates/
    └── index.html      # Starter HTML template
```

### Key files

- **`app.py`** – Instantiates `Haske`, registers routes, and calls `app.run()`. The template includes JSON and HTML handlers so you can see both response styles.
- **`models.py`** – Demonstrates how to define ORM models using the helpers from `haske.orm`. Replace this with your real database layer when ready.
- **`static/` & `templates/`** – Created only if you opt into template support during scaffolding. They work with the templating utilities documented later on.
- **`.env`** – Seeded with a debug flag and SQLite connection string so you can run the app without additional configuration.

### Extending the layout

Larger projects typically split routes, services, and schemas into dedicated packages. Because Haske is built on Starlette, you can mount sub-applications, register routers programmatically, or move to a package layout such as:

```
my_project/
├── __init__.py
├── main.py          # Creates Haske() and pulls in routers
├── routes/
│   ├── __init__.py
│   ├── api.py
│   └── pages.py
├── services/
│   └── auth.py
├── templates/
└── static/
```

Update your CLI entrypoint (`haske dev --module my_project.main:app`) or `python -m my_project.main` accordingly. The sections ahead explain how to keep each piece modular while still benefiting from Haske’s conveniences.
