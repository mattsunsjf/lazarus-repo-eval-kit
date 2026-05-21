# Coding guidelines

- Do not add obvious comments or docstrings. Only comment where the logic is non-obvious.
- Never use try/except around imports. If a dependency is required, import it at the top level and let it fail loudly if missing.
- Never add "Ultraworked with" or "Co-authored-by" lines to commit messages.

## Cursor Cloud specific instructions

This is a single-service Python 3.13 CLI tool (no web server, no database, no Docker).

**Activating the environment:** `source /workspace/.venv/bin/activate`

**Standard commands** (see also `README.md` and `.github/workflows/ci.yml`):
- Lint: `ruff check .` and `ruff format --check .`
- Test: `PYTHONPATH=$PYTHONPATH:. pytest --cov=eval_kit --cov=repo_evaluator --cov-report=term`
- Run CLI: `PYTHONPATH=$PYTHONPATH:. python repo_evaluator.py <owner/repo> --token $GITHUB_TOKEN [flags]`

**Caveats:**
- Python 3.13 is required (`.python-version`). The base VM ships 3.12; the update script installs 3.13 from the deadsnakes PPA and creates a venv at `.venv`.
- `PYTHONPATH` must include `.` (the workspace root) for imports to resolve when running `repo_evaluator.py` or pytest.
- Tests are fully offline (mocked API calls via fixture repos and JSON cassettes). No API keys needed for `pytest`.
- Running the CLI against a real repo requires `GITHUB_TOKEN` (or equivalent platform token) and an LLM API key in `.env`. To skip LLM-dependent stages: `--skip-quality-llm --skip-taxonomy --skip-pr-rubrics`.
- Test fixture git repos under `tests/fixtures/repos/` are built on-demand by `conftest.py` via `tests/fixtures/build_fixture_repos.py`. They are gitignored and rebuilt automatically — do not commit them.
