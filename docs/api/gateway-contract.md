# Gateway Contract

The API Gateway is the mandatory entry point for external API consumers, future web clients, Power Apps, and Power Automate. It routes public `/api/v1/` requests, validates access tokens, applies rate limits, and propagates correlation and trusted identity context.

It must not own business data or act as the only authorization layer. Direct internal service APIs are not public endpoints and require service authentication. Microsoft Fabric uses controlled analytical pipelines instead of the gateway.
