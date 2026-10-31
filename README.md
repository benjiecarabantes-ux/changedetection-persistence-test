# changedetection.io – Persistent Data Test

# Author: Benjie P. Carabantes

## Overview

This project demonstrates how to configure changedetection.io with persistent Docker storage to preserve saved watches and settings when a container is removed and recreated.

## Environment

- Application: changedetection.io
- Docker Desktop
- Docker Compose
- Local environment: Windows with WSL 2
- Application URL: http://localhost:5000

## Problem

Docker containers have their own writable filesystem. If application data is stored only inside a container without a persistent volume, that data can be lost when the container is removed and recreated.

## Proposed Solution

I configured a named Docker volume and mounted it to /datastore, where changedetection.io stores its application data.

Docker Compose Configuration

services:
  changedetection:
    image: ghcr.io/dgtlmoon/changedetection.io
    container_name: cd-test
    ports:
      - "5000:5000"
    volumes:
      - changedetection-data:/datastore
    restart: unless-stopped

volumes:
  changedetection-data:

## Testing Procedure
1. Started changedetection.io using docker compose up -d.
2. Added a test watch using https://example.com.
3. Set the custom title to Persistence Test Watch.
4. Set the group tag to Persistence Test.
5. Removed the container using docker compose down.
6. Recreated the container using docker compose up -d.
7. Reopened the dashboard and checked whether the saved watch and custom settings remained.

# Test Results

Result: PASSED

The following data remained available after the container was recreated:

- Test watch: Persistence Test Watch
- Group tag: Persistence Test
- Existing sample watches

The container also started successfully and its status was verified using:

docker compose ps

# Conclusion

The test demonstrates that mounting a named Docker volume to /datastore preserves changedetection.io application data when the container is removed and recreated.

This provides a working example of persistent storage configuration using Docker Compose.

Limitation: The actual cause of the reported issue on the employer's server has not yet been confirmed because its Docker configuration and datastore were not available for inspection. The production environment should be checked to verify the /datastore volume mount, storage permissions, and related configuration.