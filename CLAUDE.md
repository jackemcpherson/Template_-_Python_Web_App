# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Copier template for creating FastAPI + FastHTML web applications. The template generates production-ready Python web services that combine FastAPI for JSON APIs with FastHTML for server-rendered UI, using modern tooling and best practices.

## Essential Commands

### Development Setup
- `make install` - Install dependencies and set up development environment using uv
- `make help` - Show all available commands

### Code Quality
- `make lint` - Run Ruff formatter and linter
- `make test` - Run pytest with coverage (generates htmlcov/ directory)
- `make security` - Run Bandit security scanner on src/ directory
- `make check-all` - Run all quality checks (lint, test, security)

### Development Server
- `make run-dev` - Start Uvicorn development server with hot-reload at http://127.0.0.1:8000

### Docker Operations
- `make docker-build` - Build production Docker image
- `make docker-run` - Run application in Docker container

### Direct uv Commands
- `uv sync --all-extras` - Synchronize all dependencies
- `uv run pytest -k <filter>` - Run specific tests
- `uv run ruff format .` - Format code
- `uv run ruff check .` - Lint code
- `uv run mypy` - Type checking
- `uv run bandit -r src` - Security scanning

## Architecture & Structure

### Template System
- This is a **Copier template repository**, not a regular application
- Template files use Jinja2 syntax with variables like `{{ package_name }}` and `{{ project_slug }}`
- Generated projects follow the structure: `src/{{ package_name }}/` for application code, `tests/` for tests
- Key template variables: `project_name`, `package_name`, `project_slug`, `author_name`, `author_email`, `initial_version`, `github_username`

### Generated Project Layout
- **Core runtime**: `src/{{ package_name }}/main.py` contains the FastHTML app with `fast_app()` and root route
- **Configuration**: `pyproject.toml` centralizes metadata, dependencies, and tool configuration
- **Quality gates**: Makefile wraps uv commands for consistent development workflow
- **Testing**: `tests/test_main.py` uses FastAPI's TestClient to test both JSON and HTML responses

### Technology Stack
- **Python 3.12+** with strict typing and modern syntax
- **FastAPI 0.111** for JSON APIs
- **FastHTML** for server-rendered UI components
- **uv** as the single toolchain for dependency management and virtual environments
- **Ruff** for formatting and linting (replaces black/flake8)
- **mypy** in strict mode with FastHTML-specific overrides
- **pytest** with coverage reporting
- **Bandit** for security scanning

## Development Guidelines

### Code Style
- Follow PEP 8 with 4-space indentation
- Use comprehensive type hints (FastAPI signatures serve as request contracts)
- Package/module names must be lowercase snake_case for template compatibility
- All tool configuration is centralized in `pyproject.toml`

### Testing Strategy
- Place shared fixtures in `tests/conftest.py`
- Name test modules `test_*.py` and functions `test_*`
- Maintain coverage above configured thresholds
- Use TestClient for both API and HTML response testing

### Template Development
- `AGENTS.md` contains repository-specific guidelines and coding conventions
- CI generates a sample project and runs full quality checks on every push
- `copier.yml` defines template variables and generation logic
- Template files exclude the main README.md via `_exclude` configuration

### Security Practices
- Never include credentials in template defaults
- Use environment variables or `.env.local` for secrets
- Bandit scans only the `src/` directory, excluding tests
- Update dependencies through `uv pip compile` for reproducibility