# Apache Camel 4.20.0 YAML DSL & Camel Dashboard Deployment Instructions

This file serves as a system instruction guide for any AI Agent working in this workspace. Always read and adhere to these guidelines when creating, modifying, validating, or deploying Camel routes.

## 1. Context & Tech Stack
- **Target Framework:** Apache Camel 4.20.0
- **DSL Format:** Camel YAML DSL (typically starting with `- route: id: ...` or `- from: ...`)
- **Target Platform:** Camel Dashboard (running locally or accessible via MCP tools)

## 2. YAML DSL Code Style & Standards & AI Context
- All Camel routes should start with a metadata comment header block. This metadata is specifically used to help AI agents understand what the route does, what components and properties it uses, and learn the route context:
  ```yaml
  # metadata:
  #   camelVersion: 4.20.0
  #   description: "A short, clear description of the route's purpose"
  #   aiHints:
  #     component: "cron"
  #     schedule: "0/5 * * * * ?"
  #     properties:
  #       - "cron.message"
  #     routeId: "cron-log"
  ```
- Use property placeholders `{{property.name}}` for dynamic/configurable values. Ensure any required properties are set in Camel Dashboard before deployment.
- Keep route designs clean, modular, and use appropriate components available in Camel 4.20.0.

## 3. Workflow for Writing and Deploying Routes
Always follow this strict step-by-step pipeline when requested to build or deploy a Camel route:

### Step 1: Design and Write the YAML DSL
- Write the Camel route inside a file ending with `.camel.yaml` or `.yaml` (e.g., `my-integration.camel.yaml`).

### Step 2: Validate the Route
- **Mandatory:** Before uploading, validate the syntax using the `camel-jbang` MCP tool:
  - Call `camel_validate_yaml_dsl` with the YAML content of the route.
  - If validation errors are returned, fix them immediately.

### Step 3: Register/Upsert Service
- In Camel Dashboard, routes are grouped under services.
- Call the `services_upsert` tool from the `camel-dashboard` MCP server to create or reference a service name (e.g., `my-service`).
- Capture the returned `serviceId`.

### Step 4: Upload Route Version
- Call the `routes_upload_version` tool from the `camel-dashboard` MCP server.
- Pass the `serviceId`, the file name (e.g., `my-integration.camel.yaml`), and the full YAML content.
- Capture the returned `versionId`.

### Step 5: Deploy the Route
- Call the `routes_deploy_version` tool (or `routes_deploy_and_start`) from the `camel-dashboard` MCP server, passing the `versionId`.
- Verify that the route transitions to `Deployed` and starts running correctly.
