# Docker build

This action build an OCI image with Docker.

## Usage

```yaml
- name: Build and push image
  uses: minhthong582000/actions/docker-build@main
  with:
    # Current context
    context: "."
    # Path to Dockerfile
    dockerfile: ./Dockerfile.prod
    # Push the image after build ?
    push: false
    # List of tags
    tags: |
      test/test:pr-1
      test/test:1.0.0
    # List of labels
    labels: |
      org.opencontainers.image.description=A-collection-of-reusable-Github-Actions-workflows.
      org.opencontainers.image.source=https://github.com/minhthong582000/actions
      org.opencontainers.image.created=2023-06-12T04:04:21.122Z
```

## Scenarios

```yaml
name: Test

on:
  pull_request:
    branches:
      - "main"

permissions:
  contents: read

env:
  REGISTRY: test
  IMAGE_NAME: test

jobs:
  docker:
    runs-on: ubuntu-22.04
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v4
        with:
          # list of Docker images to use as base name for tags
          images: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          # generate Docker tags based on the following events/attributes
          tags: |
            type=ref,event=branch
            type=ref,event=pr
            type=semver,pattern={{version}}
          flavor: |
            latest=false

      - name: Build and push image
        uses: minhthong582000/actions/docker-build@main
        with:
          context: "."
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```
