# shipment-tracker-service

This is a hybrid provider repository.

It provides the AsyncAPI contract stored in the central contract repository at:

- `contracts/services/shipment-tracker-service/events/asyncapi.yaml`

It consumes the following dependency from `https://github.com/specmatic-demo/notification-service`:

- `notification-service` OpenAPI at `specs/openapi.yaml`
- `notification-service` AsyncAPI at `specs/asyncapi.yaml`

## Start dependency mocks

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

This starts the `notification-service` mocks declared in [specmatic.yaml](/Users/jaydeep/znsio/specmatic-demo/shipment-tracker-service/specmatic.yaml):

- OpenAPI mock on `localhost:5113`
- AsyncAPI mock on `localhost:5413`

## Start the service

In another terminal, run this from the `shipment-tracker-service` repository root:

```bash
docker compose up --build
```

This starts:

- `shipment-tracker-service` on `localhost:9012`
- Kafka on `localhost:5413`

The local Kafka setup creates these topics used by the service:

- `shipment.status.changed`
- `shipment.status.realtime`

The service consumes shipment status changes from Kafka, publishes realtime shipment updates to Kafka, publishes `shipping.shipped` events for `notification-service`, and calls the `notification-service` HTTP API at `localhost:5113`.

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
