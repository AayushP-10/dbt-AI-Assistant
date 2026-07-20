# 

# ```markdown

# \# ModelAtlas AI

# 

# 

# ModelAtlas AI is a self-hosted AI assistant for exploring dbt projects through natural-language questions.

# 

# It converts dbt models, metadata, documentation, SQL definitions, and lineage information into a searchable knowledge layer. Analysts and data teams can use the web application, Slack integration, or Model Context Protocol tools to locate models, understand transformations, investigate lineage, and retrieve project information without manually searching through large dbt repositories.

# 

# 

# \## Project Overview

# 

# Modern dbt projects can contain hundreds of models, dependencies, tests, source definitions, macros, and documentation files. As the project grows, analysts often spend significant time answering questions such as:

# 

# \- Which model contains a particular business metric?

# \- How is a reporting table calculated?

# \- What models exist within a specific schema?

# \- Which upstream sources affect a downstream dashboard?

# \- Where is customer revenue defined?

# \- What SQL transformation creates a particular field?

# \- Which model should be used for a new analysis?

# 

# ModelAtlas AI creates a searchable knowledge base from dbt project metadata and allows users to investigate that information using plain English.

# 

# The platform combines semantic search with structured dbt metadata retrieval. Relevant models are located through embeddings and vector similarity, while detailed model attributes such as SQL, materialization, schema, dependencies, and lineage are retrieved from the application database.

# 

# \---

# 

# \## Problem Statement

# 

# Finding information inside a large dbt project is often a manual process.

# 

# An analyst may need to search YAML files, inspect compiled SQL, trace model dependencies, read documentation, and contact data engineers before identifying the correct dataset. This creates several problems:

# 

# \- Analysts spend time locating data instead of analyzing it.

# \- Business metric definitions can become inconsistent.

# \- Existing models may be recreated because they are difficult to discover.

# \- New team members require additional onboarding support.

# \- Model ownership and lineage are not always immediately visible.

# \- Documentation becomes less useful when it is distributed across many files.

# 

# ModelAtlas AI addresses this problem by creating a centralized conversational interface over dbt project knowledge.

# 

# \---

# 

# \## Core Capabilities

# 

# \### Natural-Language Search

# 

# Users can describe the information they need without knowing the exact model name.

# 

# Example questions include:

# 

# ```text

# Which models contain monthly customer revenue?

# 

# ```

# 

# ```text

# Show me the model used for product conversion reporting.

# 

# ```

# 

# ```text

# What models depend on the raw orders source?

# 

# ```

# 

# ```text

# Explain how customer lifetime value is calculated.

# 

# ```

# 

# The system embeds the question, searches the vector database, retrieves relevant dbt models, and returns the most useful project context.

# 

# \### Semantic Model Discovery

# 

# ModelAtlas AI uses embeddings and PostgreSQL with `pgvector` to identify models that are conceptually related to a question.

# 

# This allows the platform to find useful models even when the user’s wording does not exactly match the model name or description.

# 

# \### Model-Level Metadata

# 

# The platform can expose information such as:

# 

# \-   Model name

# &#x20;   

# \-   Project name

# &#x20;   

# \-   Database and schema

# &#x20;   

# \-   Materialization type

# &#x20;   

# \-   SQL definition

# &#x20;   

# \-   Model description

# &#x20;   

# \-   Columns

# &#x20;   

# \-   Tests

# &#x20;   

# \-   Tags

# &#x20;   

# \-   Upstream dependencies

# &#x20;   

# \-   Downstream dependencies

# &#x20;   

# \-   Source relationships

# &#x20;   

# \-   Project lineage

# &#x20;   

# 

# \### dbt Project Summaries

# 

# Users can retrieve high-level information about connected dbt projects, including:

# 

# \-   Number of models

# &#x20;   

# \-   Available schemas

# &#x20;   

# \-   Model organization

# &#x20;   

# \-   Materialization patterns

# &#x20;   

# \-   Project structure

# &#x20;   

# \-   Connected data assets

# &#x20;   

# 

# \### Web-Based Chat Interface

# 

# The Next.js dashboard provides a browser-based interface for:

# 

# \-   Managing organizations

# &#x20;   

# \-   Connecting dbt projects

# &#x20;   

# \-   Configuring integrations

# &#x20;   

# \-   Selecting language and embedding models

# &#x20;   

# \-   Asking questions

# &#x20;   

# \-   Reviewing generated responses

# &#x20;   

# \-   Managing provider credentials

# &#x20;   

# 

# \### Slack Integration

# 

# Teams can connect the application to Slack and ask dbt-related questions without leaving their workspace.

# 

# \### Model Context Protocol Support

# 

# The included MCP server allows supported LLM clients to access selected dbt project capabilities through authenticated tools.

# 

# \### Asynchronous Processing

# 

# Celery workers process longer-running operations such as:

# 

# \-   Project ingestion

# &#x20;   

# \-   Metadata extraction

# &#x20;   

# \-   Embedding generation

# &#x20;   

# \-   Knowledge-base updates

# &#x20;   

# \-   Background synchronization

# &#x20;   

# 

# Redis is used as the default Celery message broker.

# 

# \----------

# 

# \## System Architecture

# 

# ```mermaid

# flowchart LR

# &#x20;   A\[dbt Project] --> B\[Project Ingestion]

# &#x20;   B --> C\[Metadata and Documentation Extraction]

# &#x20;   C --> D\[Django REST API]

# &#x20;   C --> E\[Embedding Generation]

# &#x20;   E --> F\[(PostgreSQL + pgvector)]

# 

# &#x20;   G\[Next.js Web Application] --> D

# &#x20;   H\[Slack Integration] --> D

# &#x20;   I\[MCP Client] --> J\[MCP Server]

# &#x20;   J --> D

# 

# &#x20;   D --> F

# &#x20;   D --> K\[LLM Provider]

# &#x20;   D --> L\[Celery Workers]

# &#x20;   L --> M\[Redis]

# &#x20;   L --> F

# 

# &#x20;   K --> D

# &#x20;   D --> G

# &#x20;   D --> H

# &#x20;   D --> J

# 

# ```

# 

# \### Request Flow

# 

# 1\.  A dbt project is connected through dbt Cloud, GitHub, or a local upload.

# &#x20;   

# 2\.  The backend extracts models, SQL, metadata, descriptions, and lineage.

# &#x20;   

# 3\.  Relevant project content is converted into vector embeddings.

# &#x20;   

# 4\.  Embeddings and structured metadata are stored in PostgreSQL.

# &#x20;   

# 5\.  A user asks a question through the dashboard, Slack, or an MCP client.

# &#x20;   

# 6\.  The question is converted into an embedding.

# &#x20;   

# 7\.  `pgvector` retrieves semantically relevant models.

# &#x20;   

# 8\.  The backend gathers structured model details.

# &#x20;   

# 9\.  The configured LLM uses the retrieved context to produce a response.

# &#x20;   

# 10\.  The result is returned to the user through the original interface.

# &#x20;   

# 

# \----------

# 

# \## Technology Stack

# 

# \### Backend

# 

# \-   Python 3.10+

# &#x20;   

# \-   Django

# &#x20;   

# \-   Django REST Framework

# &#x20;   

# \-   Celery

# &#x20;   

# \-   Redis

# &#x20;   

# \-   PostgreSQL

# &#x20;   

# \-   `pgvector`

# &#x20;   

# \-   `uv`

# &#x20;   

# 

# \### Frontend

# 

# \-   Next.js

# &#x20;   

# \-   React

# &#x20;   

# \-   TypeScript

# &#x20;   

# \-   NextAuth

# &#x20;   

# \-   Modern component-based user interface

# &#x20;   

# 

# \### AI and Retrieval

# 

# \-   Large language model APIs

# &#x20;   

# \-   Text embeddings

# &#x20;   

# \-   Retrieval-augmented generation

# &#x20;   

# \-   Vector similarity search

# &#x20;   

# \-   Configurable LLM providers

# &#x20;   

# \-   Configurable embedding models

# &#x20;   

# 

# \### Analytics Metadata

# 

# \-   dbt project models

# &#x20;   

# \-   dbt documentation

# &#x20;   

# \-   Model SQL

# &#x20;   

# \-   Source definitions

# &#x20;   

# \-   Model dependencies

# &#x20;   

# \-   Lineage metadata

# &#x20;   

# \-   Schema and materialization information

# &#x20;   

# 

# \### Integrations

# 

# \-   dbt Cloud

# &#x20;   

# \-   GitHub

# &#x20;   

# \-   Slack

# &#x20;   

# \-   Model Context Protocol

# &#x20;   

# \-   AWS Parameter Store

# &#x20;   

# \-   LocalStack

# &#x20;   

# 

# \### Infrastructure

# 

# \-   Docker

# &#x20;   

# \-   Docker Compose

# &#x20;   

# \-   PostgreSQL container

# &#x20;   

# \-   Redis container

# &#x20;   

# \-   Django backend container

# &#x20;   

# \-   Next.js frontend container

# &#x20;   

# \-   Celery worker

# &#x20;   

# \-   Flower task-monitoring interface

# &#x20;   

# \-   MCP server

# &#x20;   

# 

# \----------

# 

# \## Application Screens

# 

# \### Analytics Assistant

# 

# !\[Dashboard](https://chatgpt.com/c/docs/dashboard.png)

# 

# The dashboard provides a conversational interface for exploring connected dbt projects and retrieving model information.

# 

# \### Integrations

# 

# !\[Integrations](https://chatgpt.com/c/docs/integrations.png)

# 

# Projects and communication tools can be configured through the integrations interface.

# 

# \### Application Settings

# 

# !\[Settings](https://chatgpt.com/c/docs/settings.png)

# 

# Users can configure LLM providers, embedding models, credentials, and other application settings.

# 

# \----------

# 

# \## Quick Start

# 

# \### Prerequisites

# 

# Install the following before starting:

# 

# \-   Docker

# &#x20;   

# \-   Docker Compose

# &#x20;   

# \-   Git

# &#x20;   

# \-   An API key for at least one supported LLM provider

# &#x20;   

# 

# \### 1. Clone the Repository

# 

# Replace the example URL with the URL of this repository.

# 

# ```bash

# git clone https://github.com/YOUR\_GITHUB\_USERNAME/modelatlas-ai.git

# cd modelatlas-ai

# 

# ```

# 

# \### 2. Create the Environment File

# 

# ```bash

# cp .env.example .env

# 

# ```

# 

# Open `.env` and configure the required variables.

# 

# ```bash

# ${EDITOR:-vi} .env

# 

# ```

# 

# \### 3. Build and Start the Application

# 

# ```bash

# docker compose up --build -d

# 

# ```

# 

# \### 4. Run Database Migrations

# 

# ```bash

# docker compose exec backend-django \\

# &#x20; uv run python manage.py migrate

# 

# ```

# 

# \### 5. Open the Application

# 

# After all containers become healthy, open:

# 

# Service

# 

# URL

# 

# Web application

# 

# \[http://localhost:3000](http://localhost:3000/)

# 

# Django API

# 

# \[http://localhost:8000](http://localhost:8000/)

# 

# Flower task monitor

# 

# \[http://localhost:5555](http://localhost:5555/)

# 

# MCP server

# 

# \[http://localhost:8080](http://localhost:8080/)

# 

# Create an account through the web interface and complete the onboarding process.

# 

# \----------

# 

# \## Environment Configuration

# 

# Only a few variables are required for a standard local deployment.

# 

# \### Required Variables

# 

# ```bash

# NEXTAUTH\_SECRET=replace\_with\_a\_secure\_random\_value

# NEXTAUTH\_URL=http://localhost:3000

# NEXT\_PUBLIC\_API\_URL=http://localhost:8000

# 

# ```

# 

# Generate a secure value for `NEXTAUTH\_SECRET` with:

# 

# ```bash

# openssl rand -base64 32

# 

# ```

# 

# \### Common Optional Variables

# 

# Variable

# 

# Default

# 

# Purpose

# 

# `INTERNAL\_API\_URL`

# 

# `http://backend-django:8000`

# 

# Internal URL used by the frontend to communicate with the backend

# 

# `ENVIRONMENT`

# 

# `local`

# 

# Selects local, development, or production behavior

# 

# `APP\_HOST`

# 

# Not set

# 

# Adds an additional hostname to Django host and CORS configuration

# 

# `DATABASE\_URL`

# 

# Generated by Docker Compose

# 

# Connects the backend and workers to PostgreSQL

# 

# `CELERY\_BROKER\_URL`

# 

# `redis://redis:6379/0`

# 

# Configures the Celery message broker

# 

# `AWS\_ACCESS\_KEY\_ID`

# 

# Not set

# 

# AWS credential used when connecting to AWS services

# 

# `AWS\_SECRET\_ACCESS\_KEY`

# 

# Not set

# 

# AWS secret credential

# 

# `AWS\_DEFAULT\_REGION`

# 

# Not set

# 

# AWS region used by the application

# 

# \### PostgreSQL Defaults

# 

# ```bash

# POSTGRES\_DB=modelatlas

# POSTGRES\_USER=user

# POSTGRES\_PASSWORD=password

# POSTGRES\_PORT=5432

# 

# ```

# 

# To use an external PostgreSQL database with the `pgvector` extension:

# 

# ```bash

# EXTERNAL\_POSTGRES\_URL=postgresql://username:password@hostname:5432/database

# 

# ```

# 

# \----------

# 

# \## LLM Configuration

# 

# Provide credentials only for the providers you intend to use.

# 

# ```bash

# LLM\_OPENAI\_API\_KEY=your\_openai\_api\_key

# LLM\_ANTHROPIC\_API\_KEY=your\_anthropic\_api\_key

# LLM\_GOOGLE\_API\_KEY=your\_google\_api\_key

# 

# ```

# 

# Do not commit API keys or other secrets to GitHub.

# 

# The `.env` file should remain excluded through `.gitignore`.

# 

# \----------

# 

# \## First-Time Setup

# 

# After starting the application:

# 

# 1\.  Open `http://localhost:3000`.

# &#x20;   

# 2\.  Create a user account.

# &#x20;   

# 3\.  Sign in to the dashboard.

# &#x20;   

# 4\.  Create or select an organization.

# &#x20;   

# 5\.  Connect a dbt project.

# &#x20;   

# 6\.  Select the models that should be indexed.

# &#x20;   

# 7\.  Configure the LLM provider.

# &#x20;   

# 8\.  Configure the embedding provider.

# &#x20;   

# 9\.  Start the project ingestion process.

# &#x20;   

# 10\.  Wait for the models and embeddings to finish processing.

# &#x20;   

# 11\.  Ask questions through the web chat.

# &#x20;   

# 

# Background-task progress can be monitored through Flower:

# 

# ```text

# http://localhost:5555

# 

# ```

# 

# \----------

# 

# \## Connecting a dbt Project

# 

# ModelAtlas AI supports multiple project-ingestion methods.

# 

# \### dbt Cloud

# 

# For dbt Cloud, provide the requested project information and service token through the project onboarding interface.

# 

# \### GitHub

# 

# A dbt project stored in GitHub can be connected through the supported repository integration.

# 

# \### Local Upload

# 

# A local dbt project can also be uploaded as a supported archive through the application.

# 

# After the project is connected, select which models should be available for:

# 

# \-   Question answering

# &#x20;   

# \-   Embedding generation

# &#x20;   

# \-   Model retrieval

# &#x20;   

# \-   SQL verification

# &#x20;   

# \-   Project exploration

# &#x20;   

# 

# \----------

# 

# \## Example Questions

# 

# Once the project is indexed, users can ask questions such as:

# 

# ```text

# Which models calculate customer revenue?

# 

# ```

# 

# ```text

# Find models related to customer retention.

# 

# ```

# 

# ```text

# Show the SQL used in the customer\_metrics model.

# 

# ```

# 

# ```text

# Which sources feed the monthly\_sales model?

# 

# ```

# 

# ```text

# What downstream models depend on stg\_orders?

# 

# ```

# 

# ```text

# Summarize the structure of the marketing analytics project.

# 

# ```

# 

# ```text

# Which models are materialized as incremental tables?

# 

# ```

# 

# ```text

# Where is gross margin calculated?

# 

# ```

# 

# \----------

# 

# \## Slack Integration

# 

# Slack can be configured through the application dashboard.

# 

# \### Setup Process

# 

# 1\.  Open \*\*Integrations\*\*.

# &#x20;   

# 2\.  Select \*\*Slack\*\*.

# &#x20;   

# 3\.  Follow the displayed manifest instructions to create a Slack application.

# &#x20;   

# 4\.  Configure the required permissions.

# &#x20;   

# 5\.  Add the generated credentials:

# &#x20;   

# &#x20;   -   Bot token

# &#x20;       

# &#x20;   -   Signing secret

# &#x20;       

# &#x20;   -   App token

# &#x20;       

# 6\.  Install the Slack application in the selected workspace.

# &#x20;   

# 7\.  Test the connection.

# &#x20;   

# 

# After setup, users can ask project-related questions from Slack.

# 

# Some additional integrations may remain experimental and should be tested before production use.

# 

# \----------

# 

# \## MCP Server

# 

# ModelAtlas AI includes a Model Context Protocol server for exposing selected dbt project functions to compatible LLM clients.

# 

# The MCP server is designed for self-hosted deployments because each client requires access to a dedicated server instance.

# 

# \### Available MCP Tools

# 

# \#### `list\_dbt\_models`

# 

# Lists and filters dbt models using fields such as:

# 

# \-   Project

# &#x20;   

# \-   Schema

# &#x20;   

# \-   Materialization

# &#x20;   

# \-   Model name

# &#x20;   

# 

# \#### `search\_dbt\_models`

# 

# Uses semantic search to locate models related to a natural-language query.

# 

# \#### `get\_model\_details`

# 

# Returns detailed information for a selected model, including:

# 

# \-   SQL

# &#x20;   

# \-   Description

# &#x20;   

# \-   Schema

# &#x20;   

# \-   Materialization

# &#x20;   

# \-   Metadata

# &#x20;   

# \-   Dependencies

# &#x20;   

# \-   Lineage

# &#x20;   

# 

# \#### `get\_project\_summary`

# 

# Returns a high-level overview of a connected dbt project and its structure.

# 

# \----------

# 

# \## MCP Configuration

# 

# Add the following values to `.env`:

# 

# ```bash

# MCP\_AUTHORIZATION\_BASE\_URL=http://localhost:8000

# DJANGO\_BACKEND\_URL=http://localhost:8000

# ALLOWED\_ORIGINS=\*

# 

# ```

# 

# For production environments, replace `\*` with an explicit list of trusted origins.

# 

# Start the full application stack:

# 

# ```bash

# docker compose up -d

# 

# ```

# 

# Check the MCP server:

# 

# ```bash

# curl http://localhost:8080/health

# 

# ```

# 

# Test OAuth metadata discovery:

# 

# ```bash

# curl http://localhost:8080/.well-known/oauth-authorization-server

# 

# ```

# 

# View MCP server logs:

# 

# ```bash

# docker compose logs -f mcp-server

# 

# ```

# 

# \----------

# 

# \## MCP Authentication Flow

# 

# The MCP integration uses OAuth 2.0 with PKCE and organization-scoped access.

# 

# ```mermaid

# sequenceDiagram

# &#x20;   participant Client as LLM Client

# &#x20;   participant MCP as MCP Server

# &#x20;   participant API as Django Backend

# &#x20;   participant Browser as User Browser

# 

# &#x20;   Client->>MCP: Request OAuth metadata

# &#x20;   MCP->>API: Retrieve OAuth configuration

# &#x20;   API->>MCP: Return provider metadata

# &#x20;   MCP->>Client: Return OAuth metadata

# 

# &#x20;   Client->>MCP: Begin authorization with PKCE

# &#x20;   MCP->>API: Forward authorization request

# &#x20;   API->>Browser: Redirect user to login

# &#x20;   Browser->>API: Authenticate user

# &#x20;   API->>MCP: Return authorization code

# &#x20;   MCP->>Client: Forward authorization code

# 

# &#x20;   Client->>MCP: Exchange authorization code

# &#x20;   MCP->>API: Validate request and exchange code

# &#x20;   API->>MCP: Return access and refresh tokens

# &#x20;   MCP->>Client: Return tokens

# 

# &#x20;   Client->>MCP: Call tool with access token

# &#x20;   MCP->>API: Validate token and retrieve user context

# &#x20;   API->>MCP: Return organization-scoped information

# &#x20;   MCP->>Client: Return tool result

# 

# ```

# 

# \### Authentication Features

# 

# \-   OAuth 2.0 metadata discovery

# &#x20;   

# \-   PKCE-based authorization

# &#x20;   

# \-   JWT access tokens

# &#x20;   

# \-   Refresh tokens

# &#x20;   

# \-   Organization-scoped access

# &#x20;   

# \-   Token validation

# &#x20;   

# \-   Automatic token expiration

# &#x20;   

# \-   Client registration support

# &#x20;   

# 

# \----------

# 

# \## Example MCP Interactions

# 

# \### Discovering Models

# 

# ```text

# User:

# What dbt models are available?

# 

# MCP tool:

# list\_dbt\_models

# 

# Result:

# Models are returned by project, schema, and materialization.

# 

# ```

# 

# \### Semantic Model Search

# 

# ```text

# User:

# Find models related to customer revenue.

# 

# MCP tool:

# search\_dbt\_models

# 

# Result:

# The tool returns models ranked by semantic similarity.

# 

# ```

# 

# \### Retrieving Model Details

# 

# ```text

# User:

# Show me the SQL and dependencies for customer\_revenue\_monthly.

# 

# MCP tool:

# get\_model\_details

# 

# Result:

# The tool returns model metadata, SQL, materialization, and lineage.

# 

# ```

# 

# \### Summarizing a Project

# 

# ```text

# User:

# Summarize the connected marketing analytics project.

# 

# MCP tool:

# get\_project\_summary

# 

# Result:

# The tool returns the project's models, schemas, and overall structure.

# 

# ```

# 

# \----------

# 

# \## Managing the Application

# 

# \### View Running Containers

# 

# ```bash

# docker compose ps

# 

# ```

# 

# \### Open a Backend Shell

# 

# ```bash

# docker compose exec backend-django bash

# 

# ```

# 

# \### Open a Frontend Shell

# 

# ```bash

# docker compose exec frontend-nextjs sh

# 

# ```

# 

# \### Follow Backend Logs

# 

# ```bash

# docker compose logs -f backend-django

# 

# ```

# 

# \### Follow Frontend Logs

# 

# ```bash

# docker compose logs -f frontend-nextjs

# 

# ```

# 

# \### Follow Worker Logs

# 

# ```bash

# docker compose logs -f celery-worker

# 

# ```

# 

# \### Follow MCP Logs

# 

# ```bash

# docker compose logs -f mcp-server

# 

# ```

# 

# \### Stop the Application

# 

# ```bash

# docker compose down

# 

# ```

# 

# This keeps the existing Docker volumes.

# 

# \### Remove the Application and Stored Volumes

# 

# ```bash

# docker compose down -v

# 

# ```

# 

# This deletes locally stored PostgreSQL data and should be used carefully.

# 

# \----------

# 

# \## Local Development

# 

# Docker Compose is the recommended setup.

# 

# For development without Docker, install:

# 

# \-   Python 3.10+

# &#x20;   

# \-   Node.js 18+

# &#x20;   

# \-   PostgreSQL 16+

# &#x20;   

# \-   `pgvector`

# &#x20;   

# \-   `uv`

# &#x20;   

# \-   `pnpm`

# &#x20;   

# \-   Redis

# &#x20;   

# 

# \### Backend Setup

# 

# ```bash

# uv venv

# source .venv/bin/activate

# uv pip install -e backend\_django/

# 

# ```

# 

# Start the backend:

# 

# ```bash

# cd backend\_django

# uv run python manage.py migrate

# uv run python manage.py runserver 0.0.0.0:8000

# 

# ```

# 

# \### Frontend Setup

# 

# Install frontend packages:

# 

# ```bash

# pnpm install --filter frontend\_nextjs

# 

# ```

# 

# Start the frontend:

# 

# ```bash

# cd frontend\_nextjs

# pnpm dev

# 

# ```

# 

# Export the same environment variables used in the Docker-based setup.

# 

# \----------

# 

# \## Security Considerations

# 

# Before using the application in a production environment:

# 

# \-   Use HTTPS for all public endpoints.

# &#x20;   

# \-   Replace development secrets.

# &#x20;   

# \-   Restrict Django allowed hosts.

# &#x20;   

# \-   Restrict CORS origins.

# &#x20;   

# \-   Store API keys in a secure secret manager.

# &#x20;   

# \-   Avoid committing `.env` files.

# &#x20;   

# \-   Use a production-grade PostgreSQL configuration.

# &#x20;   

# \-   Restrict database-network access.

# &#x20;   

# \-   Apply organization-level authorization.

# &#x20;   

# \-   Rotate access and refresh tokens.

# &#x20;   

# \-   Monitor authentication failures.

# &#x20;   

# \-   Review MCP tool permissions.

# &#x20;   

# \-   Validate uploaded dbt project files.

# &#x20;   

# \-   Review logs for accidental sensitive-data exposure.

# &#x20;   

# \-   Apply rate limits to externally accessible endpoints.

# &#x20;   

# \-   Back up PostgreSQL data.

# &#x20;   

# \-   Test recovery procedures.

# &#x20;   

# 

# \----------

# 

# \## Troubleshooting

# 

# \### Containers Do Not Start

# 

# Inspect container status:

# 

# ```bash

# docker compose ps

# 

# ```

# 

# Review logs:

# 

# ```bash

# docker compose logs

# 

# ```

# 

# \### Database Migration Errors

# 

# Confirm that PostgreSQL is healthy:

# 

# ```bash

# docker compose ps postgres

# 

# ```

# 

# Run migrations again:

# 

# ```bash

# docker compose exec backend-django \\

# &#x20; uv run python manage.py migrate

# 

# ```

# 

# \### No dbt Models Are Returned

# 

# Confirm that:

# 

# \-   A dbt project is connected.

# &#x20;   

# \-   The project ingestion completed successfully.

# &#x20;   

# \-   Models were selected for indexing.

# &#x20;   

# \-   Background workers are running.

# &#x20;   

# \-   Embeddings were generated.

# &#x20;   

# \-   The user has access to the correct organization.

# &#x20;   

# 

# Inspect worker logs:

# 

# ```bash

# docker compose logs -f celery-worker

# 

# ```

# 

# \### LLM Requests Fail

# 

# Check that:

# 

# \-   The correct provider key is configured.

# &#x20;   

# \-   The API key is active.

# &#x20;   

# \-   The selected model is available.

# &#x20;   

# \-   The account has sufficient provider quota.

# &#x20;   

# \-   The backend container received the environment variable.

# &#x20;   

# 

# \### MCP Authentication Fails

# 

# Check:

# 

# \-   The Django backend is accessible.

# &#x20;   

# \-   The MCP authorization URL is correct.

# &#x20;   

# \-   The user is logged in.

# &#x20;   

# \-   OAuth metadata is available.

# &#x20;   

# \-   Redirect URLs match the client configuration.

# &#x20;   

# \-   The requested organization is accessible.

# &#x20;   

# 

# \### Port Conflict

# 

# Update the relevant port mapping in `docker-compose.yml` and modify the associated environment URLs.

# 

# \----------

# 

# \## Current Limitations

# 

# \-   The quality of answers depends on the quality of dbt documentation.

# &#x20;   

# \-   Incomplete model descriptions may reduce semantic-search accuracy.

# &#x20;   

# \-   LLM responses may still require human verification.

# &#x20;   

# \-   Provider costs depend on the selected language and embedding models.

# &#x20;   

# \-   Large projects may require additional processing time and storage.

# &#x20;   

# \-   MCP functionality is intended primarily for self-hosted deployments.

# &#x20;   

# \-   Each MCP client may require a dedicated server configuration.

# &#x20;   

# \-   Experimental integrations may change.

# &#x20;   

# \-   Generated explanations should not replace direct validation of production SQL.

# &#x20;   

# \-   Access-token and refresh-token behavior should be reviewed before production deployment.

# &#x20;   

# 

# \----------

# 

# \## What I Learned

# 

# Completing this implementation helped me understand how several modern data and AI components work together:

# 

# \-   Extracting structured information from dbt project metadata

# &#x20;   

# \-   Creating embeddings from analytics documentation

# &#x20;   

# \-   Storing and searching vectors with PostgreSQL and `pgvector`

# &#x20;   

# \-   Combining semantic retrieval with structured database queries

# &#x20;   

# \-   Building retrieval-augmented generation workflows

# &#x20;   

# \-   Connecting a Django REST backend with a Next.js frontend

# &#x20;   

# \-   Processing ingestion and embedding tasks asynchronously with Celery

# &#x20;   

# \-   Using Redis as a message broker

# &#x20;   

# \-   Running a multi-service application with Docker Compose

# &#x20;   

# \-   Connecting AI applications to enterprise analytics metadata

# &#x20;   

# \-   Exposing analytics capabilities through MCP tools

# &#x20;   

# \-   Implementing OAuth-based access for external AI clients

# &#x20;   

# \-   Understanding the importance of organization-scoped access

# &#x20;   

# \-   Managing LLM and embedding-provider configuration

# &#x20;   

# 

# \----------

# 

# \## Potential Future Enhancements

# 

# Possible extensions include:

# 

# \-   Evaluating semantic-search precision using a labeled question set

# &#x20;   

# \-   Adding model-ranking metrics such as precision at K and recall at K

# &#x20;   

# \-   Creating automated tests for generated answers

# &#x20;   

# \-   Adding user feedback to improve model retrieval

# &#x20;   

# \-   Detecting undocumented dbt models

# &#x20;   

# \-   Identifying duplicate or overlapping business metrics

# &#x20;   

# \-   Adding column-level semantic search

# &#x20;   

# \-   Expanding model-lineage visualization

# &#x20;   

# \-   Introducing role-based permissions for MCP tools

# &#x20;   

# \-   Adding query and response audit logs

# &#x20;   

# \-   Measuring response latency and token usage

# &#x20;   

# \-   Supporting local embedding models

# &#x20;   

# \-   Supporting local language models

# &#x20;   

# \-   Adding deployment templates for AWS, Azure, or GCP

# &#x20;   

# \-   Creating monitoring dashboards for ingestion failures

# &#x20;   

# \-   Adding automated synchronization with dbt Cloud

# &#x20;   

# \-   Detecting stale documentation and broken model references

# &#x20;   

