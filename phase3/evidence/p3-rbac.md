# P3. API user with minimum permissions

Run against my real Wazuh 4.14.9 manager (`https://localhost:55000`). The user `lab-readonly` had a random password that only lived in memory during the run. No tokens or passwords are saved here. The user, role and policy were deleted at the end.

## Policy

```json
{
  "actions": [
    "manager:read",
    "decoders:read",
    "rules:read",
    "logtest:run"
  ],
  "resources": [
    "*:*:*",
    "decoder:file:*",
    "rule:file:*"
  ],
  "effect": "allow"
}
```

The documentation of the RBAC reference asks for specific resources for some actions (`decoder:file:*` for `decoders:read`, `*:*` for `manager:read` and `logtest:run`), so I kept `*:*:*`, which covers `*:*`, added `decoder:file:*`, and added `rule:file:*` by analogy, because I could not confirm it in the documentation. The role contains only this policy, and the user only this role.

## Calls made with the `lab-readonly` token

| Call | Expected | HTTP | Response (trimmed) |
|---|---|---|---|
| `GET /manager/info` | should work | 200 | Basic information was successfully read; version v4.14.9 |
| `GET /rules/files?relative_dirname=etc/rules` | should work | 200 | All rule files were returned; files ['local_rules.xml'] |
| `PUT /logtest` (SSH log) | should work | 200 | decoder sshd, rule 5710 level 5, alert True |
| `PUT /rules/files/p3_test.xml` | should fail (permissions) | 403 | Permission Denied / Permission denied: Resource type: *:* |

## Cleanup

- Deleted: users `lab-readonly` (HTTP 200), roles `lab-readonly-role` (HTTP 200), policies `lab-readonly-policy` (HTTP 200).
- Still exist after deletion: users False, roles False, policies False.
