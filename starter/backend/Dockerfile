FROM public.ecr.aws/docker/library/python:3.10-slim-bookworm

ENV FLASK_RUN_HOST=0.0.0.0

# Set working directory
WORKDIR /app

# Create non-root user
RUN adduser --uid 1000 --disabled-password --gecos "" app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    gcc \
    libpcre3 \
    libpcre3-dev \
    libssl-dev \
    libffi-dev \
    linux-headers-amd64 \
    && pip install --no-cache-dir pipenv \
    && rm -rf /var/lib/apt/lists/*

# Copy application code
COPY Pipfile Pipfile.lock /app/
RUN pipenv install --system --deploy

COPY . /app

# Change ownership to non-root user
RUN chown -R app:app /app

# Drop privileges
USER app

# Run uWSGI using module mode
CMD ["uwsgi", "--http", "0.0.0.0:5000", "--master", "--enable-threads", "--module", "app:app"]
