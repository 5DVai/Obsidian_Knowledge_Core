# Hasura SME Manual: A Comprehensive Guide to Enterprise-Grade GraphQL Engine
**Source:** Hasura SME manual questions.pdf  
**Ingestion Date:** 2025-11-28

## Executive Summary
The Hasura SME Manual provides an in-depth exploration of the Hasura GraphQL Engine, a powerful tool that transforms databases into real-time GraphQL and REST APIs. This document is a treasure trove of architectural insights, configuration guidelines, and operational best practices for deploying Hasura in enterprise environments. It covers a wide array of topics, including security, performance tuning, observability, and compliance, making it an invaluable resource for organizations looking to leverage Hasura for scalable and secure data access.

Hasura's ability to auto-generate GraphQL schemas and its support for advanced features like remote schema stitching, event triggers, and scheduled jobs position it as a leading solution for modern data-driven applications. The manual also delves into the enterprise-specific enhancements available in Hasura Cloud, such as observability dashboards, rate limiting, and compliance with standards like SOC2 and HIPAA. This comprehensive guide is essential for architects and developers aiming to implement robust, scalable, and secure GraphQL APIs.

## Key Concepts & Principles
- **GraphQL to SQL Compilation:** Hasura translates GraphQL queries directly into SQL, eliminating the need for custom resolvers.
- **Role-Based Access Control (RBAC):** Fine-grained permissions are enforced using roles and session variables.
- **Event Triggers and Actions:** Enable integration with external systems through webhooks and custom business logic.
- **Observability and Metrics:** Built-in support for Prometheus metrics and OpenTelemetry for distributed tracing.
- **Security and Compliance:** Features include JWT authentication, TLS encryption, and compliance with SOC2, HIPAA, and ISO standards.

## Detailed Technical Analysis

### Architectural Patterns
Hasura employs a microservices-friendly architecture, acting as a Backend for Frontend (BFF) that unifies multiple data sources into a single GraphQL API. This design supports seamless integration with existing microservices through remote schemas and actions, enabling a federated data model.

### Security and Compliance
Security is a cornerstone of Hasura's design, with robust mechanisms for authentication and authorization. JWT-based authentication ensures secure access, while RBAC allows for precise control over data visibility. The manual emphasizes the importance of disabling introspection in production to prevent schema exposure and recommends regular rotation of admin secrets.

### Performance and Scalability
The manual provides detailed guidance on tuning Hasura for high performance, including connection pooling strategies and live query optimization. It highlights the use of PgBouncer for managing database connections and discusses the benefits of horizontal scaling through Kubernetes.

### Observability and Diagnostics
Hasura's observability features are designed to provide deep insights into system performance and health. The integration with Prometheus and Grafana allows for real-time monitoring of key metrics, while OpenTelemetry support facilitates comprehensive tracing of request flows.

## Enterprise Q&A Bank
1. **What is the default Hasura GraphQL endpoint path?**
   - `/v1/graphql` is the main endpoint for GraphQL requests.

2. **How can I limit API usage per user in Hasura?**
   - Configure rate limits using a sliding window per role or IP, with Redis support for Enterprise.

3. **How does Hasura enforce permissions on queries?**
   - Through role-based permissions and dynamic session variables in JWTs.

4. **What is a Hasura Action?**
   - A mechanism to extend Hasura’s schema with custom logic via external webhooks.

5. **How are Event Triggers delivered?**
   - Delivered at least once, relying on DB triggers to enqueue events.

6. **How to enable GraphQL query caching in Hasura?**
   - Use the `@cached` directive on queries in Hasura Cloud.

7. **What logging types can Hasura emit?**
   - Includes `http-log`, `websocket-log`, and `webhook-log`.

8. **How to expose a GraphQL query as a REST endpoint?**
   - Define a URL path in metadata that maps to a saved GraphQL query.

9. **What metrics does Hasura expose for Prometheus?**
   - Metrics include `hasura_graphql_requests_total` and `hasura_graphql_execution_time_seconds`.

10. **How do I apply Hasura metadata changes in CI?**
    - Use `hasura metadata apply` to push changes during CI/CD.

## Actionable Takeaways
- **Security Best Practices:**
  - Disable introspection in production.
  - Regularly rotate admin secrets and JWT keys.
  - Use HTTPS for all communications.

- **Performance Optimization:**
  - Implement connection pooling with PgBouncer.
  - Optimize live query settings for high subscription loads.

- **Operational Excellence:**
  - Monitor key metrics using Prometheus and Grafana.
  - Set up alerting for critical events and performance thresholds.

- **Compliance and Governance:**
  - Ensure compliance with SOC2, HIPAA, and ISO standards.
  - Maintain an allowlist for approved queries in production.

- **Scalability Strategies:**
  - Leverage Kubernetes for horizontal scaling.
  - Use remote schemas for federated data access.

---

**Raw Content:**  
--- title: "SME Manual — Hasura" compiled_at_utc: "2025-10-07T00:00:00Z" source_count: 18 version_window: "2023-01-01 → 2025-09-30" focus: "Developer-first usage, reliability, security, performance, observability" plan_policy: "Ignore plans unless upgrade/downgrade clearly improves outcomes" --- Executive Summary Hasura is a real-time GraphQL and REST engine that sits atop your database (Postgres/MySQL/SQL Server/Snowflake etc.) and auto-generates a GraphQL schema, queries, mutations, and subscriptions . It supports authorization, caching, event triggers, scheduled jobs, and remote schema stitching . In enterprise/cloud mode, Hasura adds features like observability dashboards, rate limiting, distributed tracing, and advanced security (SOC2/HIPAA/ISO certifications) . Plan Recommendation: Most core features are available in Hasura OSS. Enterprise/Cloud adds monitoring, SLAs, and compliance. If you need certified compliance (SOC2, HIPAA, ISO) or managed hosting, use Hasura Cloud/Enterprise . Otherwise, Community Edition with robust deployments (Kubernetes, Docker Swarm) suffices for most use cases. Golden Paths (90-Minute Mastery) Local Quickstart: Install Hasura (Docker or CLI). Connect a Postgres database and run hasura migrate apply . The engine auto-generates GraphQL types/queries/mutations/subscriptions for tracked tables . Use the CLI or Docker logs to inspect logs and start the console ( hasura console ) . First Query: Open Hasura Console (usually http://localhost:9695/console). In GraphiQL, run a simple query like { users { id name } } on a tracked table. Verify it returns data from your DB. Authentication & Permissions: Add an auth header or JWT. Define roles and permission rules via Console or metadata. Test that unauthorized fields/rows are blocked. (Hasura uses attribute-based RBAC , via roles and x-hasura-* session vars .) Deploy to Prod: Configure environment variables (e.g. HASURA_GRAPHQL_DATABASE_URL , HASURA_GRAPHQL_ADMIN_SECRET ). Run Hasura Engine (Docker/K8s). Secure endpoint (HTTPS, auth) and set HASURA_GRAPHQL_ENABLED_LOG_TYPES as needed . Monitor & Scale: Enable Prometheus metrics (self-hosted Enterprise) or use Cloud built-in dashboards . Set up scaling: run multiple Hasura instances behind a load balancer (cloud provider autoscaling or Kubernetes). Tune connection pools and CPU/RAM as workload grows . 1 2 3 4 3 4 1. 2 5 2. 3. 6 4. 7 5. 8 9 10 1