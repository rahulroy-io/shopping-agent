# This image is a lightweight Linux environment with micromamba preinstalled.
# Think of it as a "portable development computer" for this project.
FROM mambaorg/micromamba:2.0.8-debian12-slim

# MAMBA_DOCKERFILE_ACTIVATE=1:
# Makes "base" conda environment available during Docker build steps.
#
# PYTHONDONTWRITEBYTECODE=1:
# Prevents Python from creating .pyc files (keeps mounted folder cleaner).
#
# PYTHONUNBUFFERED=1:
# Forces immediate log output (useful when reading container logs).
ENV MAMBA_DOCKERFILE_ACTIVATE=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Copy dependency list into image.
# The path is relative to the Compose build context (repo root).
COPY docker/dev/environment.yml /tmp/environment.yml

# Install all declared dependencies into micromamba "base" environment.
# Then clean package caches to keep image smaller.
RUN micromamba install -y -n base -f /tmp/environment.yml \
    && micromamba clean --all --yes

# Default folder when you open the container shell.
WORKDIR /workspace

# Keep container process alive so this acts like a long-running dev workspace.
# Actual project install/run behavior is configured in compose.dev.yaml.
CMD ["bash", "-lc", "sleep infinity"]
