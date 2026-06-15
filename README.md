# shipment-tracker-service

This repository currently provides its own specs from the central contract repository.

Its `notification-service` OpenAPI dependency is consumed from the `migrated_to_federated_repo` branch of:

- `https://github.com/specmatic-demo/notification-service`

The consumed spec path is:

- `specs/openapi.yaml`

## Start the dependency mock

Run this from the `shipment-tracker-service` repository root:

```bash
docker run --rm -it \
  -v "$(pwd):/usr/src/app" \
  -v ~/.specmatic:/root/.specmatic \
  -w /usr/src/app \
  --network=host \
  specmatic/enterprise \
  mock
```

This starts the `notification-service` OpenAPI mock on `localhost:5113`.

## Start the service

In another terminal, run this from the `shipment-tracker-service` repository root:

```bash
docker compose up --build
```

This starts:

- `shipment-tracker-service` on `localhost:9012`
- Kafka on `localhost:5413`

The service expects the `notification-service` mock to already be running at `localhost:5113`.

## Run contract tests

In a third terminal, run this from the `shipment-tracker-service` repository root:

```bash
docker run --rm -it \
  -v "$(pwd):/usr/src/app" \
  -v ~/.specmatic:/root/.specmatic \
  -w /usr/src/app \
  --network=host \
  specmatic/enterprise \
  test
```

The generated reports will be written under:

- `build/reports/specmatic`

## Send the service test report to Insights

After the test run completes, run this from the `shipment-tracker-service` repository root:

```bash
docker run -it \
  -v "$(pwd):/usr/src/app" \
  -v ~/.specmatic:/root/.specmatic \
  -w /usr/src/app \
  --network=host \
  specmatic/specmatic \
  send-report \
  --branch-name=main \
  --repo-name="$(gh repo view --json name -q .name)" \
  --repo-id="$(gh api 'repos/{owner}/{repo}' --jq .id)" \
  --repo-url="$(gh repo view --json url --jq .url)"
```
