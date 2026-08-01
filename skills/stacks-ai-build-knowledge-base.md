---
name: Build and populate a StackAI knowledge base
description: Create a StackAI knowledge base, upload and index files into it, trigger a sync, and list its resources.
api: openapi/stacks-ai-openapi-original.yml
operations:
  - createKnowledgeBase
  - uploadKnowledgeBaseResource
  - syncKnowledgeBase
  - listKnowledgeBaseResources
  - getKnowledgeBaseResource
---

# Build and populate a StackAI knowledge base

Use this skill to stand up a retrieval knowledge base and load documents into it.

## Auth
Send `Authorization: Bearer <API_KEY>` on every request (knowledge-base endpoints use HTTP bearer).
See `authentication/stacks-ai-authentication.yml`.

## Steps
1. **Create the knowledge base** — `createKnowledgeBase`: `POST /v1/knowledge-bases`.
   - Body: `name`, optional `description`, `indexing_params` (embedding model default `openai.text-embedding-3-large`,
     chunker `chunk_size`/`chunk_overlap`), and optional `connection_id`/`website_sources`.
   - Returns the new `knowledge_base_id` (uuid).
2. **Upload a file** — `uploadKnowledgeBaseResource`: `POST /v1/knowledge-bases/{knowledge_base_id}/resources`.
   - `multipart/form-data` with a `file` part. Returns `resource_id`; indexing starts automatically (HTTP 202).
3. **Poll a resource** — `getKnowledgeBaseResource`: `GET /v1/knowledge-bases/{knowledge_base_id}/resources/{resource_id}`.
   - Check `status` in {resource, parsed, indexed, pending, pending_delete, deleted, error} until `indexed`.
4. **Sync when a connected source changes** — `syncKnowledgeBase`: `POST /v1/knowledge-bases/{knowledge_base_id}/sync` (HTTP 202).
5. **List resources** — `listKnowledgeBaseResources`: `GET /v1/knowledge-bases/{knowledge_base_id}/resources`.
   - Cursor pagination: `cursor`, `page_size` (max 100), `direction` (`next` = older, `prev` = newer).

## Rules
- Validation failures return HTTP 422 (`{"detail":[...]}`), see `errors/stacks-ai-problem-types.yml`.
- Uploads are asynchronous — always confirm `status: indexed` before relying on the file for retrieval.
- Pagination and conventions: see `conventions/stacks-ai-conventions.yml`.
