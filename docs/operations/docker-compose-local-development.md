# Docker Compose Local Development

Docker Compose is the initial local runtime for the gateway, each service, and their separately owned PostgreSQL databases. Multiple databases may run on one local PostgreSQL server or container, but every service receives exclusive credentials and network/database access only to its own database. Schema-only isolation is not sufficient.

Kubernetes, an event broker, and production cloud infrastructure are not part of this phase. Future infrastructure decisions belong in ADRs.
