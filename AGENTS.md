# kafka-consumer-blueprint

Spring Boot Kafka consumer reference service (order and payment event listeners, DLT error handling, actuator health and metrics).

Org-wide standards come from the `engineering-standards` Devin plugin; this file adds repo-specific rules. Human contributor guide: `CONTRIBUTING.md`.

## Setup
- Runtime: Java 21, Maven 3.9+, Spring Boot 4.0.x (`pom.xml`)
- Local Kafka: `docker-compose up -d`
- If Maven Central rate-limits (HTTP 429), add a mirror in `~/.m2/settings.xml`.

## Commands
- Build: `mvn clean install`
- Test: `mvn test`
- Run: `mvn spring-boot:run`

## Dependencies & CVE fixes
- Dependency file: `pom.xml`; Spring-managed versions come from `spring-boot-starter-parent`, so prefer bumping the parent over pinning individual Spring artifacts.
- Override a transitive version via `<dependencyManagement>` or a `<properties>` version key.
- Inspect: `mvn dependency:tree -Dincludes=<groupId>:<artifactId>`
- Audit: `mvn org.owasp:dependency-check-maven:check`

## Repo-specific rules
- Offsets are committed manually (`enable-auto-commit=false`, `ack-mode=MANUAL_IMMEDIATE`); acknowledge only after processing succeeds.
- Failed records go through `DefaultErrorHandler` + `DeadLetterPublishingRecoverer` in `config/KafkaConsumerConfig.java`; do not catch-and-ack failures in listeners.
- Never put broker credentials in `application.properties`; use env vars.
- Java style: Google Java Style, 120-char lines, Javadoc on public methods.
