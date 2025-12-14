FROM public.ecr.aws/docker/library/python:3.10-slim-bookworm

ENV FLASK_RUN_HOST=0.0.0.0

# Set working directory
WORKDIR /

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

RUN mkdir -p /var/www/app && mkdir /app

# Copy application code
COPY Pipfile Pipfile.lock /app/
RUN cd /app && pipenv install --system --deploy

COPY . /app

# Change ownership to non-root user
RUN chown -R app:app /app

# Drop privileges
USER app

# Run uWSGI using module mode
CMD ["uwsgi", "--http", "0.0.0.0:5000", "--master", "--enable-threads", "--chdir", "/app", "--module", "app:app"]
