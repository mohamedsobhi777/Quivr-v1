# Using a slim version for a smaller base image
FROM python:3.11-slim-bullseye

ARG DEV_MODE
ENV DEV_MODE=$DEV_MODE

# Install GEOS library, Rust, and other dependencies, then clean up
RUN apt-get update && apt-get install -y \
    libgeos-dev \
    libcurl4-openssl-dev \
    libssl-dev \
    pandoc \
    binutils \
    curl \
    git \
    build-essential && \
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y && \
    rm -rf /var/lib/apt/lists/* && apt-get clean

# Add Rust binaries to the PATH
ENV PATH="/root/.cargo/bin:${PATH}"

WORKDIR /code

# Copy just the requirements first
COPY ./requirements.txt .

# Upgrade pip
RUN pip install --upgrade pip

# Increase timeout to wait for the new installation
RUN pip install --no-cache-dir -r requirements.txt --timeout 200

RUN if [ "$DEV_MODE" = "true" ]; then pip install --no-cache debugpy --timeout 200; fi

# Copy the rest of the application
COPY . .

CMD ["uvicorn", "main:app", "--reload", "--host", "0.0.0.0", "--port", "5050", "--workers", "6"]