# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Documentation and Resources

- **Contributing guide**: `docs/source/developer/contributing.md` (build, test, lint commands)
- **Repository structure**: `docs/source/developer/repo.md` (monorepo layout, packages)
- **Design patterns**: `docs/source/developer/patterns.md` (Lumino patterns, signals, disposables)
- **Extension plugin examples**: `packages/*-extension/src/index.ts`
- **TypeScript Style Guide**: https://github.com/jupyterlab/jupyterlab/wiki/TypeScript-Style-Guide

## Technology Stack

- Frontend: TypeScript, React 18, Lumino (widgets), CodeMirror 6
- Backend: Python, Jupyter Server, Tornado, Traitlets
- Build: Rspack, TypeScript, Lerna (independent versioning)
- Package Manager: Yarn via `jlpm` wrapper (locked version in `jupyterlab/yarn.js`)

## Code Quality Rules

### Logging

- **Don't**: Use `console.log()` for user-facing messages
- **Do**: Use `console.warn()` for non-critical warnings during development
- **Do**: Use `console.error()` for low-level error details not shown in UI

### Type Safety

- **Do**: Define explicit TypeScript interfaces
- **Don't**: Use the `any` type; prefer `unknown` with type guards
- **Do**: Prefer type guards over type casts

### Import Paths

- **Do**: Import from package entry points: `import { Widget } from '@lumino/widgets'`
- **Don't**: Import from internal paths: `import { Widget } from '@lumino/widgets/lib/widget'`

### React Components

- **Do**: Use functional components with hooks for new code
- **Do**: Follow existing patterns in `@jupyterlab/ui-components`

## Naming Conventions

**TypeScript** (in `packages/*/src/*.ts` files):

- Classes/interfaces: `PascalCase` (e.g., `NotebookPanel`, `IDocumentManager`)
- Functions/variables: `camelCase` (e.g., `activatePlugin`, `currentWidget`)
- Constants: `UPPER_SNAKE_CASE` (e.g., `PLUGIN_ID`, `DEFAULT_TIMEOUT`)
- Private members: prefix with underscore (e.g., `_isDisposed`, `_onModelChanged`)
- Use 2-space indentation

**Python** (in `jupyterlab/*.py` files):

- Follow PEP 8 with 4-space indentation
- Classes: `PascalCase` (e.g., `LabApp`, `ExtensionManager`)
- Functions/variables: `snake_case` (e.g., `get_app_dir`, `build_check`)

For Lumino patterns (disposables, signals, commands, etc.), see `docs/source/developer/patterns.md`.

## Key File Locations

- Extension plugin patterns: `packages/*-extension/src/index.ts`
- Settings schemas: `packages/*/schema/*.json`
- Jest test helpers: `testutils/` (`@jupyterlab/testutils`)
- UI test framework: `galata/`

## Cloud-specific instructions

### Environment

- `$HOME/.local/bin` must be on `PATH` for `jlpm`, `jupyter-lab`, `pytest`, and other pip-installed scripts.
- Node.js 22 and Python 3.12 are pre-installed; no version manager changes needed.
- The `python` command is not available; use `python3` instead (or invoke via `python3 -m`).

### Running services

- **Dev server**: `jupyter lab --dev-mode --no-browser --port=8888 --ip=0.0.0.0 --IdentityProvider.token='' --ServerApp.allow_remote_access=True`
- The red stripe at the top of the page is expected in dev-mode (unreleased version indicator).
- No external databases or Docker containers are required.

### Build workflow

See `docs/source/developer/contributing.md` for full details. The key commands from the repo root:

1. `pip install -e ".[dev,test]"` — install Python package + dev/test deps
2. `jlpm install` — install JS dependencies
3. `jlpm run build` — full dev build (runs integrity, builds all packages and dev_mode bundle)
4. After changing a single TS package, rebuild just the metapackage: `cd packages/metapackage && jlpm run build`

### Lint

- `jlpm lint:check` — runs prettier, eslint, and stylelint checks
- `jlpm eslint:check`, `jlpm prettier:check`, `jlpm stylelint:check` — individual checks
- Python linting uses `ruff` (configured in `pyproject.toml`)

### Testing

- **Python**: `python3 -m pytest jupyterlab/tests` (some network-dependent tests like `test_announcements.py` may fail in sandboxed environments)
- **JS (single package)**: `cd packages/<pkg> && jlpm run build:test && jlpm test --runInBand`
- **JS (all)**: `jlpm test` from repo root (runs via lerna, can be slow)
- **UI/Galata**: `cd galata && jlpm test` (requires a running JupyterLab server)
