# Data Modeling & Persistence Bible

**Source:** 20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingested From:** .source_truth/20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingestion Date:** 2025-11-26

## Summary

This document covers the foundation of software applications: defining data structures and storing them reliably. It details the trade-offs between **Consistency** (ACID) and **Availability** (BASE), the progression from Conceptual to Physical models, and the selection of appropriate storage technologies (Relational, NoSQL, Vector). It emphasizes **PostgreSQL** as the "advanced default" and advocates for type-safe, generated data access layers.

## Core Concepts

### 1. Modeling Hierarchy

- **Conceptual:** The "What". Entities and relationships (e.g., User, Order).
- **Logical:** The "How" (Abstract). Attributes, keys, normalization.
- **Physical:** The "How" (Concrete). Tables, columns, indexes, specific DBMS types.

### 2. Consistency Models (CAP Theorem)

- **ACID (Strong Consistency):** Atomicity, Consistency, Isolation, Durability.
  - **Use Case:** Financials, critical business data.
  - **Tech:** PostgreSQL, MySQL, SQL Server.
- **BASE (Eventual Consistency):** Basically Available, Soft state, Eventual consistency.
  - **Use Case:** High-volume feeds, social likes, distributed systems.
  - **Tech:** DynamoDB, Cassandra, MongoDB.

### 3. Normalization vs. Denormalization

- **OLTP (Transactional):** Normalize to **3NF** (Third Normal Form) to eliminate redundancy and update anomalies.
- **OLAP (Analytical):** Denormalize (Star/Snowflake Schema) to optimize for heavy read/aggregation performance.

## Technology Stack

### 1. Relational (RDBMS)

- **PostgreSQL:** The "Advanced Default". Supports JSONB, Geospatial (PostGIS), and Vectors (pgvector).
- **SQLite:** Excellent for embedded, mobile, and small-scale apps.

### 2. NoSQL

- **Document:** MongoDB, DynamoDB. Flexible schema, horizontal scaling.
- **Key-Value:** Redis. High-speed caching, session storage.
- **Graph:** Neo4j. Highly connected data (social networks, fraud detection).

### 3. AI & Vector Databases

- **Purpose:** Store high-dimensional embeddings for Semantic Search and RAG.
- **Dedicated:** Milvus, Pinecone, Weaviate.
- **Integrated:** pgvector (PostgreSQL extension) - often the best starting point to keep stack simple.

## Data Access Layer (DAL)

- **Modern Approach:** Move away from reflection-heavy ORMs (Hibernate) towards **Code Generation**.
- **Tools:**
  - **Prisma:** Schema-first, type-safe client generation.
  - **sqlc:** SQL-first, generates Go/Python code from raw SQL queries.
- **Anti-Pattern:** The **N+1 Query Problem** (fetching a list, then fetching details for each item individually).

## Standard Patterns

- **Audit Columns:** Every table should have `created_at` and `updated_at`.
- **Soft Deletes:** Use `deleted_at` (nullable timestamp) instead of physical deletion for recoverability.
- **Multi-Tenancy:**
  - **Row-Level:** Shared schema, `tenant_id` column. (Cheapest, hardest isolation).
  - **Schema-Per-Tenant:** Shared DB, separate schemas. (Good balance).
  - **DB-Per-Tenant:** Physical separation. (Most secure, expensive).

## Operational Best Practices

- **Migrations:** Version control for database schema. NEVER change schema manually in production.
- **Zero-Downtime Migrations:** Use the **Expand/Contract** pattern (Add new column -> Dual write -> Backfill -> Switch reads -> Remove old column).
