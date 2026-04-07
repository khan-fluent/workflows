# khan-fluent/workflows

> Reusable GitHub Actions workflows shared across all khan-fluent repos.

## Available workflows

| Workflow | Purpose |
|---|---|
| [`ecs-deploy.yml`](.github/workflows/ecs-deploy.yml) | Build Docker image, push to ECR, deploy to ECS service |
| [`terraform.yml`](.github/workflows/terraform.yml) | Terraform plan (PR) / apply (push) with optional secret pass-through |
| [`node-ci.yml`](.github/workflows/node-ci.yml) | Lint + build for Node + React (Vite) apps with `app/{client,server}` layout |

## Versioning

Pin to a tag, not `@main`:

```yaml
uses: khan-fluent/workflows/.github/workflows/ecs-deploy.yml@v1
```

The `v1` tag tracks the latest non-breaking 1.x release. Pin to `@v1.0.0` for full reproducibility.

## Usage

### ECS deploy

```yaml
name: Deploy
on:
  push:
    branches: [main]
    paths: [app/**]
  workflow_dispatch:

jobs:
  deploy:
    uses: khan-fluent/workflows/.github/workflows/ecs-deploy.yml@v1
    with:
      ecr_repository: my-app
      ecs_cluster: my-cluster
      ecs_service: my-app
    secrets: inherit
```

Required repo secrets: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`.

### Terraform

```yaml
name: Infrastructure
on:
  pull_request:
    branches: [main]
    paths: [terraform/**]
  push:
    branches: [main]
    paths: [terraform/**]

jobs:
  plan:
    if: github.event_name == 'pull_request'
    uses: khan-fluent/workflows/.github/workflows/terraform.yml@v1
    with:
      apply: false
    secrets: inherit

  apply:
    if: github.event_name == 'push'
    uses: khan-fluent/workflows/.github/workflows/terraform.yml@v1
    with:
      apply: true
    secrets: inherit
```

Required: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`. Optional pass-through: `ANTHROPIC_API_KEY`, `JWT_SECRET`, `SLACK_CLIENT_ID`, `SLACK_CLIENT_SECRET` (mapped to `TF_VAR_*` env vars automatically).

### Node CI

```yaml
name: CI
on:
  pull_request:
    branches: [main]
    paths: [app/**, docker-compose.yml]

jobs:
  ci:
    uses: khan-fluent/workflows/.github/workflows/node-ci.yml@v1
    with:
      docker_image_tag: my-app-test
```

---

Maintained by [khanfluent](https://khanfluent.digital).
