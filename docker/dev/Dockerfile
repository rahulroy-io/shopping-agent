# Ubuntu 24.04 base image for the dev container.
# This acts like a portable Linux workstation for local development.
FROM ubuntu:24.04

# Prevent apt prompts during image build.
ENV DEBIAN_FRONTEND=noninteractive

# Default micromamba root location (used if you choose to create environments later).
ENV MAMBA_ROOT_PREFIX=/opt/micromamba

# PYTHONDONTWRITEBYTECODE=1:
# Prevents Python from creating .pyc files (keeps mounted folder cleaner).
#
# PYTHONUNBUFFERED=1:
# Forces immediate log output (useful when reading container logs).
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Install core development tools and Python from Ubuntu repositories.
# "python3-pip" is required because compose startup installs project in editable mode.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    python3 \
    python3-pip \
    python3-venv \
    bzip2 \
    git \
    curl \
    ca-certificates \
    openssl \
    && rm -rf /var/lib/apt/lists/*

# Install micromamba binary so conda-style workflows are available on Ubuntu too.
RUN curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest \
    | tar -xvj -C /usr/local/bin --strip-components=1 bin/micromamba

# Create an isolated virtual environment for project package installs.
# This avoids Ubuntu's "externally-managed-environment" restriction (PEP 668).
RUN python3 -m venv /opt/venv \
    && /opt/venv/bin/python -m pip install --upgrade pip setuptools wheel

# Make venv Python/pip the default in later RUN/CMD/compose commands.
ENV PATH="/opt/venv/bin:${PATH}"

# Default folder when you open the container shell.
WORKDIR /workspace

# Keep container process alive so this acts like a long-running dev workspace.
# Actual project install/run behavior is configured in compose.dev.yaml.
CMD ["bash", "-lc", "sleep infinity"]
