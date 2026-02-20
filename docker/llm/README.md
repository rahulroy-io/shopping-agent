# Local LLM Server Guide (vLLM)

This folder runs a local model server that exposes an OpenAI-compatible API.

## What This Gives You

- A local inference server at `http://localhost:8000/v1`
- Compatibility with clients expecting OpenAI-style endpoints
- Reusable model cache on your host disk

## File In This Folder

- `compose.vllm.yaml`: Starts vLLM with GPU support and model arguments.

## Prerequisites

- Docker Desktop installed and running
- NVIDIA GPU with recent drivers
- NVIDIA Container Toolkit configured for Docker
- Enough VRAM for selected model

## Important Path Setup

In `compose.vllm.yaml`, update this host path if needed:

```yaml
volumes:
  - C:/Users/raulr/workdir-local/docker/hf-cache:/root/.cache/huggingface
```

Left side is your local Windows folder for model cache.
If the folder does not exist, create it first.

## Start the LLM Server

From repository root:

```powershell
docker compose -f docker/llm/compose.vllm.yaml up -d
```

Follow logs:

```powershell
docker logs -f vllm-qwen3vl
```

## Verify It Is Running

Check OpenAI models endpoint:

```powershell
curl http://localhost:8000/v1/models
```

If healthy, you should get JSON listing served model(s).

## Stop the Server

```powershell
docker compose -f docker/llm/compose.vllm.yaml down
```

## Using With Dev Container

The dev container uses:

- `LLM_BASE_URL=http://host.docker.internal:8000/v1`

So if this vLLM service is running on host port `8000`, your app in `docker/dev` can call it directly.

## Customizing the Model

In `compose.vllm.yaml`, edit `command`:

- First token: model ID (for example `Qwen/Qwen3-VL-8B-Instruct-FP8`)
- Other flags: sequence length, GPU memory utilization, precision, etc.

After editing, restart:

```powershell
docker compose -f docker/llm/compose.vllm.yaml down
docker compose -f docker/llm/compose.vllm.yaml up -d
```

## Troubleshooting

Container exits immediately
- Check logs: `docker logs vllm-qwen3vl`.
- Common causes: invalid model name, insufficient GPU memory.

GPU not detected
- Confirm NVIDIA drivers are installed.
- Confirm Docker can access GPU with NVIDIA toolkit.

Very slow first startup
- Normal: first run downloads model files.
- Later runs are faster due to cache volume.
