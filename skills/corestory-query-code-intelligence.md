---
name: Query CoreStory code intelligence for a project
description: Retrieve a project's generated PRD and technical specification from CoreStory, then open a conversation to ask grounded questions about the codebase.
api: openapi/corestory-openapi-original.json
operations:
- list_projects
- get_project
- get_prd
- get_tech_spec
- create_conversation
- list_conversations
---

# Query CoreStory code intelligence

CoreStory reverse-engineers a persistent PRD, technical specification, and knowledge
graph from a codebase. Use this skill to pull that intelligence for a project and
ask grounded questions.

## Authentication

- Base URL: `https://c2s.corestory.ai`
- All calls require `Authorization: Bearer <JWT>` (Clerk-issued). Programmatic access
  can use an API key created via `POST /api/api-keys`.
- Agents typically reach the same data through the hosted MCP server at
  `https://c2s.corestory.ai/mcp` (mcp_-prefixed token) instead of raw HTTP.

## Steps

1. **Find the project.** Call `list_projects` (`GET /api/projects`) and select the
   target `project_id`.
2. **Confirm it is ready.** Call `get_project` (`GET /api/projects/{project_id}`) and
   check status before reading generated documents.
3. **Read the PRD.** Call `get_prd` (`GET /api/projects/{project_id}/prd`) for the
   generated product-requirements document; use `get_prd_section` for one section.
4. **Read the technical spec.** Call `get_tech_spec`
   (`GET /api/projects/{project_id}/tech-spec`) for the architecture/behavior spec.
5. **Ask questions.** Open a conversation with `create_conversation`
   (`POST /api/projects/{project_id}/chat/conversations/`), then send messages to
   query the codebase in natural language. Use `list_conversations` to resume.

## Conventions and errors

- Pagination is offset/limit (`limit`, `offset`, `page`, `page_size`) — see
  `conventions/corestory-conventions.yml`.
- No idempotency-key contract is documented; retries on writes are not idempotent.
- Errors are JSON with standard HTTP status codes (404 project-not-found is the most
  common; 422 carries FastAPI validation detail) — see
  `errors/corestory-problem-types.yml`.
