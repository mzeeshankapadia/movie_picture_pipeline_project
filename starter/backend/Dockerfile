FROM public.ecr.aws/docker/library/python:3.10-slim

ENV FLASK_RUN_HOST=0.0.0.0

WORKDIR /

# Create non-root user (Debian-compatible)
RUN adduser --uid 1000 --disabled-password --gecos "" app

# Install system dependencies (Debian equivalents of apk packages)
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

RUN mkdir -p /var/www/app /app

# Install Python dependencies
COPY Pipfile Pipfile.lock /app/
RUN cd /app && pipenv install --system --deploy

# Copy application code
COPY . /app

# Drop privileges
USER app

# Run uWSGI
CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "-s", "/var/www/app/app.sock", "--manage-script-name", "--mount", "/=app:app"]
