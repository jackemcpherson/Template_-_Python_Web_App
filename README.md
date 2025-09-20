# Modern FastAPI + FastHTML Copier Template

This repository hosts an opinionated [Copier](https://copier.readthedocs.io/) template for bootstrapping high-performance Python web services. The generated projects pair **FastAPI** for JSON APIs with **FastHTML** for server-rendered UI, and lean on **uv** as the single toolchain for dependency management, scripting, and reproducible builds. The template bakes in strict linting, type checking, security scanning, and CI so new services can focus on product work instead of setup.

## Highlights
- **Modern stack** – FastAPI 0.111, FastHTML (via `python-fasthtml`), Uvicorn with standard extras, and Python 3.12 targeting.
- **Rust-powered tooling** – uv for environments, Ruff for lint/format, and mypy in strict mode (with FastHTML-specific overrides).
- **Security and quality gates** – Bandit scans the application package, pytest ships with coverage reporting, and CI exercises the same commands the Makefile exposes.
- **Container-first** – Multi-stage Dockerfile installs dependencies once with uv and produces a non-root runtime image.
- **Developer UX** – A concise Makefile wraps every common task (`make install`, `make lint`, `make test`, `make run-dev`, etc.).
- **Copier ergonomics** – Only a handful of prompts (`project_name`, `project_description`, `author_name`, `author_email`, `initial_version`) keep scaffolding fast while enabling personalized metadata.

## Generated Project Layout
```
{{ project_name }}/
├── .dockerignore
├── .github/workflows/ci.yml
├── .gitignore
├── Dockerfile
├── Makefile
├── README.md
├── copier.yml
├── pyproject.toml
├── src/
│   └── {{ package_name }}/
│       ├── __init__.py
│       └── main.py
└── tests/
    ├── __init__.py
    └── test_main.py
```

### Key Runtime Files
- **`src/{{ package }}/main.py`** – Instantiates the FastHTML application (`fast_app()`), registers the root route, and returns a `Titled` FastTag so FastHTML auto-converts to an HTML response. The module keeps a runnable `serve()` guard for ad‑hoc debugging.
- **`tests/test_main.py`** – Uses FastAPI's `TestClient` plus httpx status codes to assert the rendered markup and status code. The assertions establish a baseline for the hybrid JSON/HTML stack.
- **`pyproject.toml`** – Centralizes metadata, dependencies, and tool configuration. Production dependencies live in `[project]`, dev tooling in `[project.optional-dependencies.dev]`, Ruff enforces `select = ["ALL"]` with targeted ignores, mypy stays in strict mode with FastHTML overrides, pytest enables coverage, and Bandit scans only the application package.
- **`Makefile`** – Wraps uv commands: `make install` → `uv sync --all-extras`, `make lint` → Ruff format & lint, `make test`, `make security`, `make check-all`, `make run-dev`, and Docker build/run helpers.
- **`Dockerfile`** – Two stages: a builder installs uv, resolves dependencies once, and the final slim image copies only the virtualenv plus `src/`, adds a non-root user, and launches Uvicorn.
- **`.github/workflows/ci.yml`** – Generates a sample project via Copier, runs `uv sync --all-extras`, Ruff format & lint (read-only), mypy, Bandit (`-r src`), and pytest. CI pins `uv` via `astral-sh/setup-uv@v1` and matches the Makefile commands for parity.

## Template Prompts
| Prompt | Purpose |
| --- | --- |
| `project_name` | Human-friendly name; becomes the package path via lower/underscore transform. |
| `project_description` | Surfaces in `pyproject.toml` and the generated README intro. |
| `author_name` / `author_email` | Fill the PEP 621 `authors` table. |
| `initial_version` | Seeds the project version (defaults to `0.1.0`). |

## Using the Template Locally
1. Install uv if you haven't already (`curl -LsSf https://astral.sh/uv/install.sh | sh` or use Homebrew).
2. Generate a project:
   ```bash
   uvx copier copy gh:jackemcpherson/Template_-_Python_Web_App path/to/new-service
   ```
3. Enter the new folder and follow the generated README. Typical flow:
   ```bash
   cd path/to/new-service
   make install
   make check-all
   make run-dev  # http://127.0.0.1:8000
   ```
4. For Docker parity: `make docker-build` and `make docker-run`.

## Maintaining This Repository
- `SPEC.md` is the canonical documentation for the template itself and is excluded from generated projects via the `_exclude` entry in `copier.yml`.
- After updating templates, regenerate a sample project and run `make check-all` to ensure lint, type, security, and test gates still pass end‑to‑end.
- CI already exercises the Copier flow on every push; monitor the latest run under **Actions → Lint and Test**.
- Cut releases by tagging commits on `main`; Copier consumers can pin to a tag for stability.

## License
This template is distributed under the MIT License (see `pyproject.toml` in generated projects for downstream metadata).
