# Spring Boot service for Kubernetes on Wodby

Build and run Spring Boot applications on Kubernetes with Wodby.

This repository defines the Wodby service manifests and operational
configuration for Spring Boot.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Start with a boilerplate

Use one of the boilerplates exposed by this service to start with compatible
build configuration and Wodby CI:

- [Spring Boot boilerplate](https://github.com/wodby/spring-boot-boilerplate)

The included boilerplate uses Spring Boot 4 and supports Java 21 and 25.

## Wodby stacks using this service

- [Spring Boot application stack](https://github.com/wodby/stack-spring-boot)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `spring-boot` |
| Type | Application service |
| Inherits from | [`java`](https://github.com/wodby/service-java) with version constraint `^1.0.0` |
| Application build | Git source connection enabled; Dockerfile: `Dockerfile`; boilerplates: Spring Boot boilerplate |
| Readiness | `/actuator/health` answers 200 |

## Use this service

Use this service through [Spring Boot application stack](https://github.com/wodby/stack-spring-boot), or reference
`spring-boot` from a custom Wodby stack.

A service is a reusable component and does not deploy by itself. The stack
defines its links, settings, versions, resources, and relationship to the rest
of the application.

The inherited links keep the Java service's component variables (`DB_*`,
`DATABASE_URL`, `REDIS_*`, `SMTP_*`) and add the names Spring Boot reads by
itself: `SPRING_DATASOURCE_URL` (a JDBC URL without credentials),
`SPRING_DATASOURCE_USERNAME`, `SPRING_DATASOURCE_PASSWORD`,
`SPRING_DATA_REDIS_HOST`, `SPRING_DATA_REDIS_PORT`,
`SPRING_DATA_REDIS_PASSWORD`, `SPRING_MAIL_HOST` and `SPRING_MAIL_PORT`.

Readiness requires Spring Boot Actuator. For an application without it, remove
`JAVA_READY_PATH` on the service so that any HTTP answer from `/` counts.
Environments of type `dev` set `SPRING_PROFILES_ACTIVE=dev`.

## Maintain a custom version

1. Fork this repository.
2. Edit the service manifest and referenced files.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, volume, config, and
derivative names stable unless dependent stacks and app-level overrides are
updated at the same time.

Validate the manifests with:

```bash
wodby service validate-manifest service.yml --include GUIDANCE.md --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/) and the [managed services index](https://github.com/wodby/services).

## Development workspaces

The workspace runtime comes from the Java service: the checkout is built with
its Maven or Gradle wrapper and the application restarts with the new build
whenever a file changes. Flyway or Liquibase migrations run at each start, as in
a deployment. Environments of type `dev` activate the `dev` profile.

Requires a runtime image declaring workspace contract version 1. Ordinary and development option tags must use matching revisions.
