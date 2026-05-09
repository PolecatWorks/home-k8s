FROM ghcr.io/cloudnative-pg/postgresql:16

USER root


# Install PostGIS
# Note: pgvector is already bundled in the standard CloudNativePG 16 image.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    postgresql-16-postgis-3 && \
    rm -rf /var/lib/apt/lists/*

USER 26
