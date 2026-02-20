# shopping-agent

Lightweight scaffold for a shopping-agent project in Python.

## Current Status

This repository is an early-stage starter:

- Core agent logic is a stub that echoes input.
- CLI currently runs a fixed sample query.
- LLM integration is not implemented yet (`src/agent_app/llm_client.py` is empty).
- Smoke test exists but test dependencies are not declared in `pyproject.toml`.

## Project Layout

```text
shopping-agent/
├── main.py                      # Minimal top-level entry point
├── pyproject.toml               # Project metadata (Python >= 3.12)
├── src/agent_app/
│   ├── core.py                  # run_agent(query) stub
│   ├── cli.py                   # CLI entry module
│   └── llm_client.py            # Placeholder for LLM client code
├── tests/test_smoke.py          # Basic smoke test for run_agent
└── docker/
    ├── dev/                     # Dev container setup
    └── llm/                     # Local vLLM server setup
```

## Prerequisites

- Python 3.12+
- Optional: Docker Desktop (for container workflows)

## Run Locally

Because this repo uses a `src/` layout and packaging config is still minimal, run with `PYTHONPATH=src`:

```bash
PYTHONPATH=src python -m agent_app.cli
```

Expected output:

```text
shopping-agent received: buy milk and bread
```

You can also run the simple top-level script:

```bash
python main.py
```

## Run With Docker Dev Container

Start the development container:

```bash
docker compose -f docker/dev/compose.dev.yaml up --build -d
```

Open a shell in the container:

```bash
docker exec -it aiml-dev-container bash
```

Inside container, run:

```bash
PYTHONPATH=src python -m agent_app.cli
```

Notes:

- `docker/dev/compose.dev.yaml` currently contains Windows-specific host paths; update them for your machine.
- The compose startup command attempts `python -m pip install -e .` if `pyproject.toml` exists.

## Optional: Local LLM Server (vLLM)

If you want an OpenAI-compatible local endpoint at `http://localhost:8000/v1`:

```bash
docker compose -f docker/llm/compose.vllm.yaml up -d
```

Then verify:

```bash
curl http://localhost:8000/v1/models
```

Before running, update Windows-specific cache mount paths in `docker/llm/compose.vllm.yaml`.

## Testing

Smoke test file:

```text
tests/test_smoke.py
```

Current test dependency gap:

- `pytest` is not listed in `pyproject.toml`, so `python -m pytest` will fail until `pytest` is installed.

## Next Milestones

- Implement `llm_client.py` and wire it into `run_agent`.
- Add a user-input CLI mode (arguments or prompt).
- Add proper packaging config for `src/` layout and declared dev dependencies (`pytest`).
