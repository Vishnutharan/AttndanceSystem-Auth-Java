# Attendance System – Auth API (Spring Boot + JWT)

Spring Boot REST API that provides JWT-based login for an attendance system. A user posts a username and password to `/api/auth/login`; if the credentials match, the API returns a signed JWT that is validated by a JWT filter on subsequent requests. Users are currently defined in memory (no database is used).

## Tech stack

- Java 17, Spring Boot 3.3.5
- Spring Security, Spring Web
- JJWT 0.11 (`io.jsonwebtoken`)
- Maven (Maven Wrapper included)

## Folder structure

```text
AttndanceSystem-Auth-Java/
├── AttndanceSystem/
│   ├── .mvn/
│   │   └── wrapper/
│   ├── src/
│   │   ├── main/
│   │   └── test/
│   ├── mvnw
│   ├── pom.xml
│   └── README.md
├── .editorconfig
├── .gitignore
└── README.md
```

The Maven project lives in `AttndanceSystem/` (package `com.example.attendance`):

| Package | Purpose |
| --- | --- |
| `controller` | `AuthController` – `POST /api/auth/login` |
| `service` | `JwtService` – creates and validates tokens (1 hour expiry) |
| `filter` | `JwtAuthFilter` – reads the `Authorization: Bearer …` header |
| `config` | `SecurityConfig` (security rules, in-memory user, password encoder), `WebConfig` (CORS) |
| `model` | `AuthRequest` / `AuthResponse` DTOs |

## API

| Method | Path | Body | Response |
| --- | --- | --- | --- |
| POST | `/api/auth/login` | `{ "username": "...", "password": "..." }` | `{ "token": "..." }` |

`/api/auth/**` is public; every other endpoint requires a valid bearer token. CORS allows `http://localhost:4200`.

## Prerequisites

- JDK 17 or newer

## Configuration

`src/main/resources/application.properties` sets the port (`8082`) and disables DataSource auto-configuration, so no database is needed even though JPA and the MySQL driver are on the classpath.

## Build and run

```bash
cd AttndanceSystem
./mvnw spring-boot:run          # Windows: mvnw.cmd spring-boot:run → http://localhost:8082
./mvnw package                  # builds target/AttndanceSystem-0.0.1-SNAPSHOT.jar
```

## Security notes

- The JWT signing key is hard-coded in `JwtService.java`, and the demo user is hard-coded in `SecurityConfig.java`. Before using this beyond local development, move both into configuration (e.g. `application.properties` placeholders backed by environment variables such as `JWT_SECRET`) and use a long random key.
