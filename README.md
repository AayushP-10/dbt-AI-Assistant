````markdown
# ModelAtlas AI

ModelAtlas AI is a self-hosted AI assistant for exploring dbt projects through natural-language questions.

It converts dbt models, metadata, documentation, SQL definitions, and lineage information into a searchable knowledge layer. Analysts and data teams can use the web application, Slack integration, or Model Context Protocol tools to locate models, understand transformations, investigate lineage, and retrieve project information without manually searching through large dbt repositories.

---

## Project Overview

Modern dbt projects can contain hundreds of models, dependencies, tests, source definitions, macros, and documentation files. As the project grows, analysts often spend significant time answering questions such as:

- Which model contains a particular business metric?
- How is a reporting table calculated?
- What models exist within a specific schema?
- Which upstream sources affect a downstream dashboard?
- Where is customer revenue defined?
- What SQL transformation creates a particular field?
- Which model should be used for a new analysis?

ModelAtlas AI creates a searchable knowledge base from dbt project metadata and allows users to investigate that information using plain English.

The platform combines semantic search with structured dbt metadata retrieval. Relevant models are located through embeddings and vector similarity, while detailed model attributes such as SQL, materialization, schema, dependencies, and lineage are retrieved from the application database.

---

## Problem Statement

Finding information inside a large dbt project is often a manual process.

An analyst may need to search YAML files, inspect compiled SQL, trace model dependencies, read documentation, and contact data engineers before identifying the correct dataset. This creates several problems:

- Analysts spend time locating data instead of analyzing it.
- Business metric definitions can become inconsistent.
- Existing models may be recreated because they are difficult to discover.
- New team members require additional onboarding support.
- Model ownership and lineage are not always immediately visible.
- Documentation becomes less useful when it is distributed across many files.

ModelAtlas AI addresses this problem by creating a centralized conversational interface over dbt project knowledge.

---

## Core Capabilities

### Natural-Language Search

Users can describe the information they need without knowing the exact model name.

Example questions include:

```text
Which models contain monthly customer revenue?
````

```text
Show me the model used for product conversion reporting.
```

```text
What models depend on the raw orders source?
```

```text
Explain how customer lifetime value is calculated.
```

The system embeds the question, searches the vector database, retrieves relevant dbt models, and returns the most useful project context.

### Semantic Model Discovery

ModelAtlas AI uses embeddings and PostgreSQL with `pgvector` to identify models that are conceptually related to a question.

This allows the platform to find useful models even when the user’s wording does not exactly match the model name or description.

### Model-Level Metadata

The platform can expose information such as:

* Model name
* Project name
* Database and schema
* Materialization type
* SQL definition
* Model description
* Columns
* Tests
* Tags
* Upstream dependencies
* Downstream dependencies
* Source relationships
* Project lineage

### dbt Project Summaries

Users can retrieve high-level information about connected dbt projects, including:

* Number of models
* Available schemas
* Model organization
* Materialization patterns
* Project structure
* Connected data assets

### Web-Based Chat Interface

The Next.js dashboard provides a browser-based interface for:

* Managing organizations
* Connecting dbt projects
* Configuring integrations
* Selecting language and embedding models
* Asking questions
* Reviewing generated responses
* Managing provider credentials

### Slack Integration

Teams can connect the application to Slack and ask dbt-related questions without leaving their workspace.

### Model Context Protocol Support

The included MCP server allows supported LLM clients to access selected dbt project capabilities through authenticated tools.

### Asynchronous Processing

Celery workers process longer-running operations such as:

* Project ingestion
* Metadata extraction
* Embedding generation
* Knowledge-base updates
* Background synchronization

Redis is used as the default Celery message broker.

---

## System Architecture

```mermaid
flowchart LR
    A[dbt Project] --> B[Project Ingestion]
    B --> C[Metadata and Documentation Extraction]
    C --> D[Django REST API]
    C --> E[Embedding Generation]
    E --> F[(PostgreSQL + pgvector)]

    G[Next.js Web Application] --> D
    H[Slack Integration] --> D
    I[MCP Client] --> J[MCP Server]
    J --> D

    D --> F
    D --> K[LLM Provider]
    D --> L[Celery Workers]
    L --> M[Redis]
    L --> F

    K --> D
    D --> G
    D --> H
    D --> J
```

### Request Flow

1. A dbt project is connected through dbt Cloud, GitHub, or a local upload.
2. The backend extracts models, SQL, metadata, descriptions, and lineage.
3. Relevant project content is converted into vector embeddings.
4. Embeddings and structured metadata are stored in PostgreSQL.
5. A user asks a question through the dashboard, Slack, or an MCP client.
6. The question is converted into an embedding.
7. `pgvector` retrieves semantically relevant models.
8. The backend gathers structured model details.
9. The configured LLM uses the retrieved context to produce a response.
10. The result is returned to the user through the original interface.

---

## Technology Stack

### Backend

* Python 3.10+
* Django
* Django REST Framework
* Celery
* Redis
* PostgreSQL
* `pgvector`
* `uv`

### Frontend

* Next.js
* React
* TypeScript
* NextAuth
* Modern component-based user interface

### AI and Retrieval

* Large language model APIs
* Text embeddings
* Retrieval-augmented generation
* Vector similarity search
* Configurable LLM providers
* Configurable embedding models

### Analytics Metadata

* dbt project models
* dbt documentation
* Model SQL
* Source definitions
* Model dependencies
* Lineage metadata
* Schema and materialization information

### Integrations

* dbt Cloud
* GitHub
* Slack
* Model Context Protocol
* AWS Parameter Store
* LocalStack

### Infrastructure

* Docker
* Docker Compose
* PostgreSQL container
* Redis container
* Django backend container
* Next.js frontend container
* Celery worker
* Flower task-monitoring interface
* MCP server

---

## Application Screens

### Analytics Assistant

![Dashboard](docs/dashboard.png)

The dashboard provides a conversational interface for exploring connected dbt projects and retrieving model information.

### Integrations

![Integrations](docs/integrations.png)

Projects and communication tools can be configured through the integrations interface.

### Application Settings

![Settings](docs/settings.png)

Users can configure LLM providers, embedding models, credentials, and other application settings.

---

## Quick Start

### Prerequisites

Install the following before starting:

* Docker
* Docker Compose
* Git
* An API key for at least one supported LLM provider

### 1. Clone the Repository

Replace the example URL with the URL of this repository.

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/modelatlas-ai.git
cd modelatlas-ai
```

### 2. Create the Environment File

```bash
cp .env.example .env
```

Open `.env` and configure the required variables.

```bash
${EDITOR:-vi} .env
```

### 3. Build and Start the Application

```bash
docker compose up --build -d
```

### 4. Run Database Migrations

```bash
docker compose exec backend-django \
  uv run python manage.py migrate
```

### 5. Open the Application

After all containers become healthy, open:

| Service             | URL                   |
| ------------------- | --------------------- |
| Web application     | http://localhost:3000 |
| Django API          | http://localhost:8000 |
| Flower task monitor | http://localhost:5555 |
| MCP server          | http://localhost:8080 |

Create an account through the web interface and complete the onboarding process.

---

## Environment Configuration

Only a few variables are required for a standard local deployment.

### Required Variables

```bash
NEXTAUTH_SECRET=replace_with_a_secure_random_value
NEXTAUTH_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:8000
```

Generate a secure value for `NEXTAUTH_SECRET` with:

```bash
openssl rand -base64 32
```

### Common Optional Variables

| Variable                | Default                      | Purpose                                                           |
| ----------------------- | ---------------------------- | ----------------------------------------------------------------- |
| `INTERNAL_API_URL`      | `http://backend-django:8000` | Internal URL used by the frontend to communicate with the backend |
| `ENVIRONMENT`           | `local`                      | Selects local, development, or production behavior                |
| `APP_HOST`              | Not set                      | Adds an additional hostname to Django host and CORS configuration |
| `DATABASE_URL`          | Generated by Docker Compose  | Connects the backend and workers to PostgreSQL                    |
| `CELERY_BROKER_URL`     | `redis://redis:6379/0`       | Configures the Celery message broker                              |
| `AWS_ACCESS_KEY_ID`     | Not set                      | AWS credential used when connecting to AWS services               |
| `AWS_SECRET_ACCESS_KEY` | Not set                      | AWS secret credential                                             |
| `AWS_DEFAULT_REGION`    | Not set                      | AWS region used by the application                                |

### PostgreSQL Defaults

```bash
POSTGRES_DB=modelatlas
POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_PORT=5432
```

To use an external PostgreSQL database with the `pgvector` extension:

```bash
EXTERNAL_POSTGRES_URL=postgresql://username:password@hostname:5432/database
```

---

## LLM Configuration

Provide credentials only for the providers you intend to use.

```bash
LLM_OPENAI_API_KEY=your_openai_api_key
LLM_ANTHROPIC_API_KEY=your_anthropic_api_key
LLM_GOOGLE_API_KEY=your_google_api_key
```

Do not commit API keys or other secrets to GitHub.

The `.env` file should remain excluded through `.gitignore`.

---

## First-Time Setup

After starting the application:

1. Open `http://localhost:3000`.
2. Create a user account.
3. Sign in to the dashboard.
4. Create or select an organization.
5. Connect a dbt project.
6. Select the models that should be indexed.
7. Configure the LLM provider.
8. Configure the embedding provider.
9. Start the project ingestion process.
10. Wait for the models and embeddings to finish processing.
11. Ask questions through the web chat.

Background-task progress can be monitored through Flower:

```text
http://localhost:5555
```

---

## Connecting a dbt Project

ModelAtlas AI supports multiple project-ingestion methods.

### dbt Cloud

For dbt Cloud, provide the requested project information and service token through the project onboarding interface.

### GitHub

A dbt project stored in GitHub can be connected through the supported repository integration.

### Local Upload

A local dbt project can also be uploaded as a supported archive through the application.

After the project is connected, select which models should be available for:

* Question answering
* Embedding generation
* Model retrieval
* SQL verification
* Project exploration

---

## Example Questions

Once the project is indexed, users can ask questions such as:

```text
Which models calculate customer revenue?
```

```text
Find models related to customer retention.
```

```text
Show the SQL used in the customer_metrics model.
```

```text
Which sources feed the monthly_sales model?
```

```text
What downstream models depend on stg_orders?
```

```text
Summarize the structure of the marketing analytics project.
```

```text
Which models are materialized as incremental tables?
```

```text
Where is gross margin calculated?
```

---

## Slack Integration

Slack can be configured through the application dashboard.

### Setup Process

1. Open **Integrations**.
2. Select **Slack**.
3. Follow the displayed manifest instructions to create a Slack application.
4. Configure the required permissions.
5. Add the generated credentials:

   * Bot token
   * Signing secret
   * App token
6. Install the Slack application in the selected workspace.
7. Test the connection.

After setup, users can ask project-related questions from Slack.

Some additional integrations may remain experimental and should be tested before production use.

---

## MCP Server

ModelAtlas AI includes a Model Context Protocol server for exposing selected dbt project functions to compatible LLM clients.

The MCP server is designed for self-hosted deployments because each client requires access to a dedicated server instance.

### Available MCP Tools

#### `list_dbt_models`

Lists and filters dbt models using fields such as:

* Project
* Schema
* Materialization
* Model name

#### `search_dbt_models`

Uses semantic search to locate models related to a natural-language query.

#### `get_model_details`

Returns detailed information for a selected model, including:

* SQL
* Description
* Schema
* Materialization
* Metadata
* Dependencies
* Lineage

#### `get_project_summary`

Returns a high-level overview of a connected dbt project and its structure.

---

## MCP Configuration

Add the following values to `.env`:

```bash
MCP_AUTHORIZATION_BASE_URL=http://localhost:8000
DJANGO_BACKEND_URL=http://localhost:8000
ALLOWED_ORIGINS=*
```

For production environments, replace `*` with an explicit list of trusted origins.

Start the full application stack:

```bash
docker compose up -d
```

Check the MCP server:

```bash
curl http://localhost:8080/health
```

Test OAuth metadata discovery:

```bash
curl http://localhost:8080/.well-known/oauth-authorization-server
```

View MCP server logs:

```bash
docker compose logs -f mcp-server
```

---

## MCP Authentication Flow

The MCP integration uses OAuth 2.0 with PKCE and organization-scoped access.

```mermaid
sequenceDiagram
    participant Client as LLM Client
    participant MCP as MCP Server
    participant API as Django Backend
    participant Browser as User Browser

    Client->>MCP: Request OAuth metadata
    MCP->>API: Retrieve OAuth configuration
    API->>MCP: Return provider metadata
    MCP->>Client: Return OAuth metadata

    Client->>MCP: Begin authorization with PKCE
    MCP->>API: Forward authorization request
    API->>Browser: Redirect user to login
    Browser->>API: Authenticate user
    API->>MCP: Return authorization code
    MCP->>Client: Forward authorization code

    Client->>MCP: Exchange authorization code
    MCP->>API: Validate request and exchange code
    API->>MCP: Return access and refresh tokens
    MCP->>Client: Return tokens

    Client->>MCP: Call tool with access token
    MCP->>API: Validate token and retrieve user context
    API->>MCP: Return organization-scoped information
    MCP->>Client: Return tool result
```

### Authentication Features

* OAuth 2.0 metadata discovery
* PKCE-based authorization
* JWT access tokens
* Refresh tokens
* Organization-scoped access
* Token validation
* Automatic token expiration
* Client registration support

---

## Example MCP Interactions

### Discovering Models

```text
User:
What dbt models are available?

MCP tool:
list_dbt_models

Result:
Models are returned by project, schema, and materialization.
```

### Semantic Model Search

```text
User:
Find models related to customer revenue.

MCP tool:
search_dbt_models

Result:
The tool returns models ranked by semantic similarity.
```

### Retrieving Model Details

```text
User:
Show me the SQL and dependencies for customer_revenue_monthly.

MCP tool:
get_model_details

Result:
The tool returns model metadata, SQL, materialization, and lineage.
```

### Summarizing a Project

```text
User:
Summarize the connected marketing analytics project.

MCP tool:
get_project_summary

Result:
The tool returns the project's models, schemas, and overall structure.
```

---

## Managing the Application

### View Running Containers

```bash
docker compose ps
```

### Open a Backend Shell

```bash
docker compose exec backend-django bash
```

### Open a Frontend Shell

```bash
docker compose exec frontend-nextjs sh
```

### Follow Backend Logs

```bash
docker compose logs -f backend-django
```

### Follow Frontend Logs

```bash
docker compose logs -f frontend-nextjs
```

### Follow Worker Logs

```bash
docker compose logs -f celery-worker
```

### Follow MCP Logs

```bash
docker compose logs -f mcp-server
```

### Stop the Application

```bash
docker compose down
```

This keeps the existing Docker volumes.

### Remove the Application and Stored Volumes

```bash
docker compose down -v
```

This deletes locally stored PostgreSQL data and should be used carefully.

---

## Local Development

Docker Compose is the recommended setup.

For development without Docker, install:

* Python 3.10+
* Node.js 18+
* PostgreSQL 16+
* `pgvector`
* `uv`
* `pnpm`
* Redis

### Backend Setup

```bash
uv venv
source .venv/bin/activate
uv pip install -e backend_django/
```

Start the backend:

```bash
cd backend_django
uv run python manage.py migrate
uv run python manage.py runserver 0.0.0.0:8000
```

### Frontend Setup

Install frontend packages:

```bash
pnpm install --filter frontend_nextjs
```

Start the frontend:

```bash
cd frontend_nextjs
pnpm dev
```

Export the same environment variables used in the Docker-based setup.

---

## Security Considerations

Before using the application in a production environment:

* Use HTTPS for all public endpoints.
* Replace development secrets.
* Restrict Django allowed hosts.
* Restrict CORS origins.
* Store API keys in a secure secret manager.
* Avoid committing `.env` files.
* Use a production-grade PostgreSQL configuration.
* Restrict database-network access.
* Apply organization-level authorization.
* Rotate access and refresh tokens.
* Monitor authentication failures.
* Review MCP tool permissions.
* Validate uploaded dbt project files.
* Review logs for accidental sensitive-data exposure.
* Apply rate limits to externally accessible endpoints.
* Back up PostgreSQL data.
* Test recovery procedures.

---

## Troubleshooting

### Containers Do Not Start

Inspect container status:

```bash
docker compose ps
```

Review logs:

```bash
docker compose logs
```

### Database Migration Errors

Confirm that PostgreSQL is healthy:

```bash
docker compose ps postgres
```

Run migrations again:

```bash
docker compose exec backend-django \
  uv run python manage.py migrate
```

### No dbt Models Are Returned

Confirm that:

* A dbt project is connected.
* The project ingestion completed successfully.
* Models were selected for indexing.
* Background workers are running.
* Embeddings were generated.
* The user has access to the correct organization.

Inspect worker logs:

```bash
docker compose logs -f celery-worker
```

### LLM Requests Fail

Check that:

* The correct provider key is configured.
* The API key is active.
* The selected model is available.
* The account has sufficient provider quota.
* The backend container received the environment variable.

### MCP Authentication Fails

Check:

* The Django backend is accessible.
* The MCP authorization URL is correct.
* The user is logged in.
* OAuth metadata is available.
* Redirect URLs match the client configuration.
* The requested organization is accessible.

### Port Conflict

Update the relevant port mapping in `docker-compose.yml` and modify the associated environment URLs.

---

## Current Limitations

* The quality of answers depends on the quality of dbt documentation.
* Incomplete model descriptions may reduce semantic-search accuracy.
* LLM responses may still require human verification.
* Provider costs depend on the selected language and embedding models.
* Large projects may require additional processing time and storage.
* MCP functionality is intended primarily for self-hosted deployments.
* Each MCP client may require a dedicated server configuration.
* Experimental integrations may change.
* Generated explanations should not replace direct validation of production SQL.
* Access-token and refresh-token behavior should be reviewed before production deployment.

---

## What I Learned

Completing this implementation helped me understand how several modern data and AI components work together:

* Extracting structured information from dbt project metadata
* Creating embeddings from analytics documentation
* Storing and searching vectors with PostgreSQL and `pgvector`
* Combining semantic retrieval with structured database queries
* Building retrieval-augmented generation workflows
* Connecting a Django REST backend with a Next.js frontend
* Processing ingestion and embedding tasks asynchronously with Celery
* Using Redis as a message broker
* Running a multi-service application with Docker Compose
* Connecting AI applications to enterprise analytics metadata
* Exposing analytics capabilities through MCP tools
* Implementing OAuth-based access for external AI clients
* Understanding the importance of organization-scoped access
* Managing LLM and embedding-provider configuration

---

## Potential Future Enhancements

Possible extensions include:

* Evaluating semantic-search precision using a labeled question set
* Adding model-ranking metrics such as precision at K and recall at K
* Creating automated tests for generated answers
* Adding user feedback to improve model retrieval
* Detecting undocumented dbt models
* Identifying duplicate or overlapping business metrics
* Adding column-level semantic search
* Expanding model-lineage visualization
* Introducing role-based permissions for MCP tools
* Adding query and response audit logs
* Measuring response latency and token usage
* Supporting local embedding models
* Supporting local language models
* Adding deployment templates for AWS, Azure, or GCP
* Creating monitoring dashboards for ingestion failures
* Adding automated synchronization with dbt Cloud
* Detecting stale documentation and broken model references

---

```
```
