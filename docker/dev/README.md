# Dev Container Guide

This folder defines a Docker-based development workspace for this project.

## What This Gives You

- A Linux-based Python environment that is consistent across machines.
- Your project files mounted live into the container.
- A long-running container you can "enter" like a remote dev box.
- Environment variables ready for calling an OpenAI-compatible LLM endpoint.

## Files In This Folder

- `Dockerfile`: Builds the Ubuntu 24.04 based dev image and installs Python tooling with `apt`.
- `environment.yml`: Legacy conda environment reference (currently not used by the Ubuntu Dockerfile).
- `compose.dev.yaml`: Defines how to run the dev container.

## Current Host Mount

The compose file currently mounts:

- Host: `C:/Users/raulr/OneDrive/Works/learning`
- Container: `/workspace`

Project root inside container is therefore:

- `/workspace/ai-ml-skill/shopping-agent`

## Prerequisites

- Docker Desktop installed and running.
- Ability to run `docker` and `docker compose` commands in PowerShell.

## Quick Start (Windows PowerShell)

Run from repository root:

```powershell
docker compose -f docker/dev/compose.dev.yaml up --build -d
```

Open a shell in the running container:

```powershell
docker exec -it aiml-dev-container bash
```

Inside the container, run your app:

```bash
python -m agent_app.cli
```

## How It Works

1. Docker builds the image from `docker/dev/Dockerfile`.
2. During startup, Compose runs:
   - `python -m pip install -e .` (only when `pyproject.toml` exists)
   - `python -V`
   - `sleep infinity`
3. `pip install -e .` means your local code changes are reflected immediately.
4. The container stays alive so you can attach anytime.

## Common Commands

Show logs:

```powershell
docker logs -f aiml-dev-container
```

Restart after config changes:

```powershell
docker compose -f docker/dev/compose.dev.yaml down
docker compose -f docker/dev/compose.dev.yaml up --build -d
```

Stop and remove the container/network:

```powershell
docker compose -f docker/dev/compose.dev.yaml down
```

## LLM Endpoint Notes

This dev setup sets:

- `LLM_BASE_URL=http://host.docker.internal:8000/v1`
- `LLM_API_KEY=dummy`

That means your app expects an OpenAI-compatible API on your host at port `8000`.
If you run the vLLM setup from `docker/llm`, this value is already aligned.

## Troubleshooting

`pip: command not found`
- Use `python -m pip ...` inside container. This is already handled in `compose.dev.yaml`.

`externally-managed-environment` (PEP 668)
- The image uses `/opt/venv` and sets it in `PATH`, so `python` and `pip` resolve to virtualenv binaries.
- Use `python -m pip ...`, not system-level `python3 -m pip ...`.

Container keeps restarting
- Check logs: `docker logs -f aiml-dev-container`.
- Most common cause is command failure during startup.

`pyproject.toml not found, skipping editable install`
- Informational message only.
- The container starts normally, but project package is not auto-installed.

Code changes not reflected
- Confirm volume mount exists in `compose.dev.yaml`.
- Confirm you are editing files in the same repo path you mounted.
