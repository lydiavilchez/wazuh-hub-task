# P1. Sandbox with only the manager image

Checked on my Mac with Docker Desktop (context `desktop-linux`). My own Wazuh containers were not touched.

## Command

```bash
docker run -d --name sandbox-p1 --network none wazuh/wazuh-manager:4.14.9
```

## No network

- `docker inspect` shows `NetworkMode: none`.
- Inside the container, `/sys/class/net` only lists the kernel's placeholder interfaces (`lo`, `tunl0`, `sit0`...). There is no `eth0`.

## Startup time

Seconds from `docker run` until `wazuh-control status` reports `wazuh-analysisd is running`, and until `wazuh-logtest` answers with decoding output (both happened in the same check):

| Run | analysisd running | logtest answers |
|---|---|---|
| 1 (first, polling once per second) | 14.1 s | not measured |
| 2 (new container, polling every 0.5 s) | 2.5 s | 2.5 s |

The two runs differ a lot. Run 1 also paid for my slower polling loop, so I would treat 2.5 s as the best case and 14 s as a safe upper bound until I measure more runs.

## Memory

`docker stats --no-stream sandbox-p1`:

| Moment | Memory | CPU |
|---|---|---|
| Right after startup | 523 MiB | 118 % |
| 10 s later | 533 MiB | 101 % |

CPU stays around 100 % because the container keeps retrying services that need a network (for example Filebeat). A sandbox could disable those services to save CPU.

## Test with my own decoder and rule

Files copied in with `docker cp`, owner `wazuh:wazuh`, mode `660`, **without restarting**:

- `phase2/evidence/miapp_decoder.xml` to `/var/ossec/etc/decoders/`
- `phase2/evidence/miapp_rules.xml` to `/var/ossec/etc/rules/`

Log:

```text
Nov  5 12:50:01 agente miapp[4321]: login_failed user=lydia ip=198.51.100.7 reason=bad_password
```

Before copying the files, logtest says `No decoder matched`. After copying them, with no restart, `wazuh-logtest` returns (trimmed):

```text
**Phase 2: Completed decoding.
	name: 'miapp'
	reason: 'bad_password'
	srcip: '198.51.100.7'
	srcuser: 'lydia'

**Phase 3: Completed filtering (rules).
	id: '100100'
	level: '5'
	description: 'miapp: login failed (bad password)'
	groups: '['local', 'miapp', 'authentication_failed']'

**Alert to be generated.
```

## Conclusion

A sandbox made only of the `wazuh-manager` image, with no network, starts and answers logtest in seconds, uses about 0.5 GiB of memory, and picks up uploaded decoders and rules without a restart. This supports the design in `PHASE3.md`. The memory number is for an idle manager, not for the regression corpus, which P2 covers.
