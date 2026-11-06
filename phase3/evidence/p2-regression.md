# P2. Wazuh's test bank detects a rule that silences alerts

Run in the same isolated sandbox as P1 (`wazuh/wazuh-manager:4.14.9`, `--network none`). The container was removed at the end.

## What I ran

- **Test bank:** `ruleset/testing` from the Wazuh repository, tag `v4.14.9` (`runtests.py`, `coverage.py` and `tests/`). I downloaded it on my Mac and copied it in with `docker cp`.
- **Command, inside the container:** `cd /opt/wz-testing && /var/ossec/framework/python/bin/python3 runtests.py` (Wazuh's own Python). It worked on the first try.
- **Silencing rule**, in `/var/ossec/etc/rules/silence_rules.xml` (owner `wazuh:wazuh`, mode `660`):

```xml
<group name="local,"><rule id="100200" level="0"><if_sid>5710</if_sid><description>silence</description></rule></group>
```

My `miapp` decoder and rule from P1 were also in the sandbox in both runs. They did not affect the result.

## Result before and after

| | Test files run | Log checks passed | Failed | Exit code | Time |
|---|---|---|---|---|---|
| Before (no silencing rule) | 103 | 1770 | 0 | 0 | 653 s |
| After (rule 100200 added) | 103 | 1760 | 10 | 1 | 672 s |

The runner does not run the 4 `*geoip.ini` files by default, so it ran 103 of the 107 files. It counts each log line as one check (1770), not each test section (1564).

## The 10 failures

All are in `tests/sshd.ini`. In every one, the decoder is still `sshd`, but the log now hits rule `100200` (level 0) instead of the expected SSH rule:

| Test section | Failing logs | Expected rule (level 5) | Got |
|---|---|---|---|
| `invalid user` | log 1, 3, 5, 6, 7, 8, 9 | 5710 | 100200 (level 0) |
| `SSHD Disconnected from invalid` | log 1 | 5710 | 100200 |
| `SSHD Disconnecting invalid` | log 1 | 5710 | 100200 |
| `sshd brute force rule 2` | log 1 | 5712 | 100200 |

The last one is worth noticing: silencing rule 5710 also changed the result of a test about brute force (rule 5712), which is a child of 5710. So the corpus catches the knock-on effect, not only the rule that was silenced directly.

No other test file changed.

## wazuh-logtest, before and after

Log:

```text
Nov  5 12:40:01 agente sshd[1234]: Failed password for invalid user admin from 203.0.113.5 port 52111 ssh2
```

Before the rule (trimmed):

```text
**Phase 3: Completed filtering (rules).
	id: '5710'
	level: '5'
	description: 'sshd: Attempt to login using a non-existent user'
	groups: '['syslog', 'sshd', 'authentication_failed', 'invalid_login']'
	mitre.id: '['T1110.001', 'T1021.004']'
**Alert to be generated.
```

After the rule (complete output, there is no alert line):

```text
**Phase 3: Completed filtering (rules).
	id: '100200'
	level: '0'
	description: 'silence'
	groups: '['local']'
```

## Conclusion

The test bank detects a rule that silences SSH alerts: 0 failures before, 10 after, with a clear message (`Expected: 5710. Got: 100200`, level 5 to level 0). This supports the regression step in `PHASE3.md`.

Two limits to keep in mind:

- **Time.** The full bank takes about 11 minutes with this runner, because it starts one `wazuh-logtest` process per log line. That is too slow to run on every request. The service would need to run only the test files related to the rules the proposal touches, or a faster runner, and keep the full bank for scheduled runs. `PHASE3.md` (question 4) takes this into account.
- **Scope.** The bank only covers what Wazuh's own tests cover. A silencing rule on a source with no test file would not be caught.
