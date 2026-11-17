## Persistent Memory

Use the Basic Memory MCP as the primary interface for persistent memory.

Before substantial work:
- Search Basic Memory when prior decisions, constraints, conventions, or implementation history may be relevant.
- Do not search mechanically for trivial edits.

During or after substantial work:
- Record durable information that is likely to matter in future sessions.
- Good candidates include:
  - design decisions and rationale
  - rejected alternatives and why they were rejected
  - operational constraints
  - real-world usage patterns
  - recurring problems and their solutions
- Prefer updating an existing note over creating a duplicate.
- Do not store routine implementation details, temporary task status, or information obvious from the source code.

Storage:
- Use existing folders.
- Do not create new top-level folders.
- If the appropriate location is unclear, use `inbox/`.

Use Basic Memory MCP for normal memory operations.
Direct access to the mounted memory filesystem is only a fallback for maintenance or MCP failures.

