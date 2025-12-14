FROM public.ecr.aws/docker/library/python:3.10-slim-bookworm

# Set Flask host
ENV FLASK_RUN_HOST=0.0.0.0

# Create working directory
WORKDIR /app

# Create a non-root user
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

# Copy Python dependency files
COPY Pipfile Pipfile.lock /app/

# Install Python dependencies
RUN pipenv install --system --deploy

# Copy application code
COPY . /app

# Set ownership of the app directory
RUN chown -R app:app /app

# Switch to non-root user
USER app

# Expose Flask port
EXPOSE 5000

# Run uWSGI over HTTP (no UNIX socket)
CMD ["uwsgi", "--http", "0.0.0.0:5000", "--master", "--enable-threads", "--manage-script-name", "--mount", "/=app:app"]
