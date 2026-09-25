# Stage 1: The Builder
FROM ghcr.io/astral-sh/uv:python3.12-bookworm-slim AS builder
ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy
ENV UV_HTTP_TIMEOUT=120
WORKDIR /app

# Copy the dependency files first for docker caching
COPY pyproject.toml uv.lock ./

# Copy the specific services files
COPY services/product-service/pyproject.toml services/product-service/

RUN uv venv /app/.venv
# Install dependencies for the product-service
RUN uv sync --package product-service --frozen --no-dev

#  Copy the rest of the code
COPY . .

# Stage 2: The Final Image
FROM python:3.12-slim-bookworm
WORKDIR /app

# Copy the environment and code from the builder
COPY --from=builder /app/.venv /app/.venv
COPY --from=builder /app/services /app/services

# Put the virtual environment on the path
ENV PATH="/app/.venv/bin:$PATH"

ENV PYTHONPATH="/app/services/product-service"

# Run the gRPC server.
CMD ["python", "services/product-service/main.py"]