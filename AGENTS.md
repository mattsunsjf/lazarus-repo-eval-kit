# Coding guidelines

- Do not add obvious comments or docstrings. Only comment where the logic is non-obvious.
- Never use try/except around imports. If a dependency is required, import it at the top level and let it fail loudly if missing.
- Never add "Ultraworked with" or "Co-authored-by" lines to commit messages.

## Cursor Cloud specific instructions

This repo is a **Python 3.13 CLI** (no long-running servers). There is no `docker compose` or dev server to start.

### Environment

- Pin Python with `.python-version` (3.13). System Python may be older; use **uv** to install 3.13 and a project venv at `.venv`.
- Ensure `uv` is on `PATH` (typically `$HOME/.local/bin`). Install once if missing: `curl -LsSf https://astral.sh/uv/install.sh | sh`.
- After the update script runs: `source .venv/bin/activate` and `export PYTHONPATH=.`.

### Lint / test (matches `.github/workflows/ci.yml`)

```bash
source .venv/bin/activate
export PYTHONPATH=.
pre-commit run --all-files
pytest --cov=eval_kit --cov=repo_evaluator --cov-report=term
```

Fixture git repos under `tests/fixtures/repos/` are built automatically on first `pytest` (via `tests/conftest.py`). **Git** must be installed.

### Running the product

- **Full `repo_evaluator.py` run** needs a valid platform token (`--token` / `GITHUB_TOKEN`), network, and an LLM API key in `.env` unless all LLM stages are skipped (`--skip-quality-llm --skip-taxonomy --skip-pr-rubrics`, etc.). A 401 from `api.github.com` means the token is missing or invalid.
- **Offline / no-token checks**: use `python -m eval_kit.test_runners.cli <path> --detect`, or the in-process APIs (`RepoAnalyzer`, `PRAnalyzer`) with a mocked `PlatformClient` as in `tests/test_characterization_pr.py`.
- **F2P CLI**: `python -m eval_kit.test_runners.cli <repo_path> --base <sha> --head <sha>` — requires the target repo’s language toolchain on `PATH` (see README runtime table).
