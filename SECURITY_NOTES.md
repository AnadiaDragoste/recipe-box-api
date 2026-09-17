2026-09-17

- Anonymous GET /recipes → 200 OK, returns all recipes including non-public/secret entries.
- Anonymous DELETE /recipes/1 → 204 No Content, recipe 1 permanently removed from subsequent GET /recipes results.