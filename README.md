# Notification & Event Handler Service

## Description
This microservice is responsible for handling asynchronous notifications and processing specific game events. It ensures total decoupling by offloading non-critical tasks from other services.

## Key Responsibilities
- **Notifications**: Sending emails (e.g., "New Highscore!", "Welcome Bonus") and in-game push notifications.
- **Event Processing**: Listening to Kafka events like `WelcomeBonus` or `AchievementUnlocked` to trigger side effects.

## Suggested Tech Stack
- **Language/Framework**: Java 21 + Spring Boot 3.x
- **Messaging**: Apache Kafka (Consumers) for event ingestion.
- **Email/Push**: JavaMail or SendGrid integration.

## Priority
**Medium (Post-MVP)**. This service adds a "senior touch" by demonstrating architectural decoupling and asynchronous processing of side effects.

## Integration
- Consumes events from Kafka to trigger notifications without blocking the main game flow.
