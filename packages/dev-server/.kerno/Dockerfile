# Multi-stage Dockerfile for Vendure dev-server module

# Stage 1: Base image with Node.js
FROM node:20-slim AS base

# Install system dependencies required for native modules and database clients
RUN apt-get update && apt-get install -y \
    python3 \
    make \
    g++ \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Stage 2: Production image
FROM base AS production

# Copy package files
COPY package*.json ./
COPY lerna.json ./
COPY nx.json* ./

# Copy all workspace packages
COPY packages ./packages

# Install all dependencies (dev dependencies needed for ts-node runtime)
RUN npm ci

# Create necessary runtime directories
RUN mkdir -p ./assets \
    ./test-emails \
    ./import-assets \
    ./migrations \
    ./test-plugins \
    ./dist \
    ./load-testing/data-sources \
    ./load-testing/static/assets

# Set environment variables
ENV NODE_ENV=development
ENV API_PORT=3000
ENV DB=postgres
ENV DB_HOST=localhost
ENV DB_PORT=5432
ENV DB_USERNAME=vendure
ENV DB_PASSWORD=password
ENV DB_NAME=vendure-dev
ENV DB_SCHEMA=public

# Expose default API port and admin UI port
EXPOSE 3000 5001

# Default command - run node with ts-node for TypeScript execution
# This assumes there's an index.ts or similar entry point in the dev-server package
CMD ["node", "-r", "ts-node/register", "-r", "dotenv/config", "-r", "tsconfig-paths/register", "packages/dev-server/index.ts"]
