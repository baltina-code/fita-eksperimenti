# What `make test` does

`make test` runs a single command, `pytest -q` (see `Makefile`). It runs pytest in quiet mode from the repo root. There's no `pytest.ini`, `pyproject.toml` or `setup.cfg`, so pytest uses its default discovery and collects `test_*.py` files.

Right now that finds one file, `tests/test_smoke.py`, which has two smoke tests:

1. **`test_openapi_document_can_be_loaded`** parses `docs/openapi.yaml` with `yaml.safe_load`. It then checks that the `openapi` version starts with `3.` and that `paths` isn't empty. This only confirms the API contract is valid YAML with a basic OpenAPI 3 shape. It doesn't validate the schema in depth.
2. **`test_participant_files_are_present`** checks that these files exist:
   - `.claude/settings.json`
   - `.devcontainer/devcontainer.json`
   - `CLAUDE.md`
   - `Makefile`
   - `tracker/CR-2.md`
   - `tracker/README.md`

   If any are missing, it fails and lists them ("Trūkst faili: …").

## How it compares to the other targets

- **`make verify-setup`** is a stricter environment check. It checks the Python version, installed packages (FastAPI, Pydantic v2, httpx, pytest, schemathesis), the `claude` CLI, and that `setup/claude-answer.md` exists. After that it runs only the smoke test file.
- **`make lint-contract`** runs `tools/lint_contract.py` on `docs/openapi.yaml`. This is the real contract check. Per `CLAUDE.md`, you run it in addition to `make test` whenever you change the contract.

When you add new test files under `tests/` (or anywhere pytest finds them), `make test` will pick them up automatically.
