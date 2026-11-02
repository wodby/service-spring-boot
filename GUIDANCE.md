# Spring Boot on Wodby

What this service adds to the Java service it is based on. Check it before adding datasource, Redis or mail settings to the project.

## Variables the service sets

| Variable | Meaning |
| --- | --- |
| `JAVA_READY_PATH` | `/actuator/health`. The service is ready when that path answers 200. The application needs `spring-boot-starter-actuator`; without it, remove the variable on the service so that any answer from `/` counts. |
| `SPRING_PROFILES_ACTIVE` | `dev` in environments of type `dev` only. Absent in every other type, where the default profile applies. |

## Linked services

In addition to the variables of the Java service, the links set the names Spring Boot reads by itself, so `application.properties` needs no datasource, Redis or mail connection settings:

| Link | Variables |
| --- | --- |
| Database | `SPRING_DATASOURCE_URL` (a JDBC URL such as `jdbc:postgresql://host:5432/db`, without credentials), `SPRING_DATASOURCE_USERNAME`, `SPRING_DATASOURCE_PASSWORD` |
| Redis or Valkey | `SPRING_DATA_REDIS_HOST`, `SPRING_DATA_REDIS_PORT`, `SPRING_DATA_REDIS_PASSWORD` |
| Mail | `SPRING_MAIL_HOST`, `SPRING_MAIL_PORT` |

The JDBC URL uses the linked server's own driver: `jdbc:postgresql:`, `jdbc:mariadb:` or `jdbc:mysql:`. The project needs that driver among its dependencies. Each variable is present only while its link exists and the linked service is enabled: settings in `application.properties` are overridden by them, so a local default such as `jdbc:postgresql://localhost:5432/app` is fine there. Redis is used only by an application with `spring-boot-starter-data-redis`, mail only with `spring-boot-starter-mail`. Never print or commit the password values.

## Database migrations

The service runs no migration action. Use Flyway or Liquibase, which Spring Boot runs at startup when they are among the dependencies, so every deployment migrates the database before the application serves requests. Several replicas starting together take Flyway's or Liquibase's lock in turn.

## Behind Wodby's gateway

On Kubernetes, Spring Boot trusts the gateway's `X-Forwarded-*` headers by itself, so redirects and generated links use the public host and HTTPS. Graceful shutdown is on by default; keep its timeout below the service's 30 second shutdown grace period.
