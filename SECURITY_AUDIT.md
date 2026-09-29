# Recipe API Security Audit

## Password storage
Registration stores a generated password hash in `users.password_hash`, not the plaintext password.

## Authentication and authorization tests

| Request | Identity | Expected | Observed | Database effect | Result |
|---|---|---:|---:|---|---|
| PATCH /recipes/6 | Anonymous | 401 | 401 | None | PASS |
| PATCH /recipes/6 | Invalid JWT | 401 | 401 | None | PASS |
| PATCH /recipes/6 | User B (owner) | 200 | 200 | Instructions updated | PASS |
| PATCH /recipes/6 | User C (non-owner) | 403 | 403 | None | PASS |
| DELETE /recipes/7 | Anonymous | 401 | 401 | Recipe preserved | PASS |
| DELETE /recipes/7 | User C (non-owner) | 403 | 403 | Recipe preserved | PASS |
| DELETE /recipes/7 | User B (owner) | 204 | 204 | Recipe deleted | PASS |
| PATCH /recipes/6 (old exploit replay) | User C (non-owner) | 403 | 403 | None | PASS |

## Database verification
- After the blocked PATCH attempts, recipe 6 retained owner ID 3 and instructions `Valid owner security exam test`.
- After the blocked DELETE attempts, recipe 7 still existed.
- After the owner DELETE, querying recipe 7 returned `None`.

## Conclusion
Authentication rejects missing and invalid JWTs. Authorization prevents normal users from editing or deleting another user's recipes. The previously demonstrated cross-user PATCH exploit is blocked without changing the stored record.
