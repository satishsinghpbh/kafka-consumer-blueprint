# Review rules for kafka-consumer-blueprint

Org baseline (OWASP ASVS / NIST SSDF mapped, from the `engineering-standards` plugin) applies: secrets, injection, authz, data loss, breaking changes, untested logic, vulnerable deps. Repo-specific rules below.

## Blocking
- A listener that acknowledges a record before processing succeeds, or catches an exception and acknowledges anyway.
- Processing that is not idempotent: delivery is at-least-once, so redelivered events must be safe.
- Changes to topic names, consumer `group-id`, or event model fields without a compatibility or migration note.
- Error handling that bypasses `DefaultErrorHandler` / DLT, or a retry config with no backoff limit.
- Broker or SASL/SSL credentials in `application.properties`, `docker-compose.yml` or `k8s-deployment.yaml`.
- Removing liveness/readiness probes or resource limits from `k8s-deployment.yaml`.
- New listener or service logic without unit tests; Kafka interaction changes without a `spring-kafka-test` or Testcontainers test.

## Non-blocking
- Missing Javadoc on public methods.
- Lines over 120 characters.
- Missing metrics or structured log fields on new listeners.

## Paths needing extra care
- `src/main/java/**/config/`: consumer, retry and DLT behavior.
- `src/main/resources/application.properties`: production defaults; document every new property.
- `k8s-deployment.yaml`: production deployment.
