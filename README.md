
# notification-service – Dron Wars Org

The **Notification & Event Handler microservice** for **Dron Wars**, a browser shoot 'em up heavily inspired by the NES classic *Abadox* (1989).

This service is the "side-effects engine" of the system: it listens to business events from other microservices (via Kafka) and handles all asynchronous notifications to players — emails, in-game pushes, and background rewards — keeping core services (auth, game-core, progress) lightweight and focused on their domains.

## Why this service?
- Decouples notification logic from business-critical services (fire-and-forget pattern)  
- Ensures reliable delivery of player communications (welcome emails, highscore alerts, achievement unlocks)  
- Demonstrates robust event-driven architecture with error handling and retries  
- Critical for user engagement and retention in a gamified experience

## Features & Learning Objectives

- **Java 21+**: Pattern matching for event dispatching, records for notification payloads  
- **Spring Boot 3.x**: Spring Kafka consumers with manual acknowledgment & error handling  
- **Event-Driven**: Kafka consumers for multiple topics (`user-events`, `game-events`, `achievement-events`)  
- **Email Delivery**: Templated HTML emails (Thymeleaf + JavaMail or SendGrid)  
- **In-Game Push**: Real-time notifications via WebSocket (calls Game Core or dedicated SSE/WebSocket channel)  
- **Reliability**: Dead Letter Queue (DLQ) support, retries, idempotency  
- **Async Processing**: Non-blocking consumers with virtual threads (Java 21)  
- **Testing**: JUnit 5 + Testcontainers (Kafka), embedded SMTP for email testing (GreenMail)

## Tech Stack

- Java 21  
- Spring Boot 3.3+  
- Spring Kafka (consumers + error handling)  
- Spring Boot Starter Mail + Thymeleaf (email templating)  
- Optional: SendGrid / AWS SES Java SDK  
- Lombok (optional)  
- Maven  
- Docker

## Supported Kafka Events (Examples)

| Event Name              | Source Service         | Action Taken by Notification Service                  |
|-------------------------|------------------------|-------------------------------------------------------|
| `UserRegistered`        | Auth Service           | Send "Welcome to Dron Wars!" email + initial bonus coins |
| `GameEnded`             | Game Core Service      | If new personal best → send "New Highscore!" email    |
| `AchievementUnlocked`   | Progress Service       | Push in-game notification "Achievement Unlocked!"     |
| `PasswordResetRequested`| Auth Service           | Send password reset link email                        |
| `HighscoreBeaten`       | Leaderboard Service    | Optional: Congratulatory email or push                |

## Main Implementation Points

- Kafka Consumers: `@KafkaListener` with `@Acknowledgment` for manual commit  
- Email Templating: Thymeleaf templates in `src/main/resources/templates/email/`  
- Error Handling: Custom `ErrorHandler` + DLQ topic  
- Async Execution: `@Async` or virtual threads for non-blocking delivery

## Quick Start (Local Development)

### Prerequisites
- Java 21+  
- Maven  
- Docker & Docker Compose (shared infra: Kafka)

### 1. Start Kafka infrastructure
```bash
# From monorepo root
docker-compose up -d kafka zookeeper
```

### 2. Run the service
```bash
cd notification-service
./gradlew clean build
./gradlew bootRun
```

→ Service listens on Kafka topics (no HTTP server needed by default)

### 3. Example application.yml
```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: notification-group
      enable-auto-commit: false
      auto-offset-reset: earliest
  mail:
    host: localhost
    port: 1025          # For local testing (MailHog or similar)
    properties:
      mail.smtp.auth: false
thymeleaf:
  prefix: classpath:/templates/email/
server:
  port: 8084            # Optional Actuator
```

## Project Structure

```
notification-service/
├── src/
│   ├── main/
│   │   ├── java/com/dronwars/notification/
│   │   │   ├── config/          # KafkaConsumerConfig, MailConfig
│   │   │   ├── consumer/        # UserEventConsumer, GameEventConsumer
- Pure event-driven side-effects (no REST exposure needed)  
- Reliable Kafka consumption with DLQ & retries  
- Email templating & external integration  
- Decoupling via events (no direct calls between services)  
- Virtual threads for high-volume notification bursts

Part of **dron-wars-org** → https://github.com/dron-wars-org  
Personal learning/portfolio project – MIT License  
Pablo – Don Torcuato, Buenos Aires – 2026
