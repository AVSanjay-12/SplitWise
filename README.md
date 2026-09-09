# Splitwise Backend

A production-style Splitwise-inspired backend built with Spring Boot, PostgreSQL, and Redis. The application supports expense sharing with multiple split strategies, JWT authentication, group-based authorization, settlements, Redis caching, and distributed rate limiting.

## Features

### Authentication & Security

* JWT-based authentication using Spring Security

* BCrypt password hashing

* Protected APIs with JWT authorization

* User-aware APIs using authenticated identity (`/auth/me`)

* Redis-backed distributed rate limiting for authentication endpoints

### Expense Management

* Create and manage groups

* Add members to groups

* Three split types:

  * EQUAL (automatically includes all group members)

  * EXACT

  * PERCENTAGE

* Expense history with pagination and sorting

* Automatic balance calculation

* "Who owes whom" settlement calculation

* Settle-up transactions

### Authorization

* Only group members can:

  * add expenses

  * view balances

  * view expense history

  * view group members

  * create settlements

* Split users must belong to the group

* Duplicate group memberships are prevented

### Validation

* Expense amount must be positive

* EXACT splits must match the total expense

* PERCENTAGE splits must total 100%

* Duplicate users in a split are prevented

* Self-settlement is prevented

### Infrastructure

* PostgreSQL as the primary database

* Redis for:

  * balance caching

  * distributed rate limiting

* Flyway database migrations

* Docker & Docker Compose support

* OpenAPI / Swagger documentation

* Structured application logging

## Tech Stack

Language:
Java 21

Framework:
Spring Boot
Security
Spring Security, JWT

Database:
PostgreSQL

Cache:
Redis

ORM:
Spring Data JPA (Hibernate)

Migrations:
Flyway

Documentation:
Swagger / OpenAPI

Containerization:
Docker, Docker Compose

Build Tool:
Maven

## Architecture

```
Client
   │
   ▼
Spring Boot REST API
   │
   ├── Spring Security (JWT)
   ├── Rate Limit Filter (Redis)
   ├── Service Layer
   ├── JPA/Hibernate
   ├── PostgreSQL
   └── Redis
        ├── Balance Cache
        └── Rate Limiting
```

## API Highlights

`POST /users`
Register user

`POST /auth/login`
Login and receive JWT

`GET /auth/me`
Current authenticated user

`POST /groups`
Create group

`POST /groups/{groupId}/users/{userId}`
Add member

`GET /groups/{groupId}/members`
View group members

`POST /expenses`
Create expense

`GET /expenses/groups/{groupId}/balances`
View balances

`GET /expenses/groups/{groupId}`
Expense history

`POST /expenses/settle`
Settle balances

Interactive API documentation is available through Swagger UI after the application starts.

## Running Locally

### Prerequisites

* Docker

* Docker Compose

### Start the application

Bash

```
docker compose up --build
```

This starts:

* Spring Boot

* PostgreSQL

* Redis

Flyway automatically creates the database schema during startup.

Swagger UI becomes available at:

```
http://localhost:9090/swagger-ui/index.html
```

## Configuration

The application uses environment variables for production-ready configuration.

Example:

properties

```
DB_URL=...
DB_USERNAME=...
DB_PASSWORD=...
REDIS_HOST=...
REDIS_PORT=...
JWT_SECRET=...
```

## Redis Features

### Balance Caching

Group balance calculations are cached in Redis using Spring Cache.

* First request computes balances from PostgreSQL.

* Subsequent requests are served from Redis.

* Cache is automatically invalidated when expenses or settlements change.

### Distributed Rate Limiting

Authentication endpoints are protected using Redis atomic counters.

* Maximum 5 requests per minute per IP

* Uses Redis `INCR` and `EXPIRE`

* Shared across multiple application instances

* Automatically cleans up expired counters

## Database Migrations

Flyway manages the database schema.

* Versioned SQL migrations

* Automatic migration during application startup

* Consistent schema across environments

## Testing

This project prioritizes integration testing over isolated unit testing.

The business logic is heavily integrated with Spring Security, PostgreSQL, Redis, and Flyway, so validating complete request flows provides stronger confidence than mocking individual components.

Planned integration testing stack:

* JUnit 5

* Spring Boot Test

* MockMvc

* Testcontainers with PostgreSQL

## Example Workflow

1. Register users.

2. Login to receive a JWT.

3. Create a group.

4. Add members.

5. Create expenses using:

   * EQUAL

   * EXACT

   * PERCENTAGE

6. View balances.

7. Settle outstanding amounts.

8. View updated balances.

## Future Improvements

* Integration tests with Testcontainers

* CI/CD pipeline using GitHub Actions

* Refresh token support

* Frontend integration

## Key Backend Concepts Demonstrated

* REST API design

* JWT authentication

* Group-based authorization

* DTO and mapper architecture

* Spring Security filter chain

* Redis caching

* Distributed rate limiting

* Flyway migrations

* Dockerized deployment

* Pagination and sorting

* Financial validation logic
