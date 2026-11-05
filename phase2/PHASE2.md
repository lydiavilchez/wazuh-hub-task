# Phase 2 · Finding a need

I chose to help users **write decoders and rules for logs that Wazuh doesn't understand**: the AI writes them, they are tested automatically before being handed over, and a person decides whether to install them. The full research is in [`research/RESEARCH.md`](research/RESEARCH.md), and all the sources are listed at the end of this document.

## What needs did you find and where did each one come from?

Before looking for needs I checked what already exists, so I wouldn't propose something repeated: the [Hub](https://hub.wazuh.com/docs) services (Mobius, Vesper, Pharos, Argus, Umbra, Lumen and Fleet), the Wazuh Cloud [AI Analyst](https://documentation.wazuh.com/current/cloud-service/ai-analyst.html) and what Wazuh 5.0 brings. Then I searched the Wazuh mailing list, GitHub issues, the documentation, and community tools and articles, giving priority to what was published in 2026.

| Need | Where it comes from |
|---|---|
| **1. Writing decoders and rules for logs Wazuh doesn't understand** | It is the most repeated problem on the mailing list, also in 2026: [Check Point](https://groups.google.com/g/wazuh/c/jisvjBlUBdY), [OpenStack](https://groups.google.com/g/wazuh/c/GMazaGqQbe4), [Dovecot](https://groups.google.com/g/wazuh/c/fPfi7dJoaeQ), [an OLT fibre device](https://groups.google.com/g/wazuh/c/1V8J7s-bx2k) or [a Sophos firewall](https://groups.google.com/g/wazuh/c/U66uWEwDOBw). There is a [GitHub issue open for years asking for Google Workspace](https://github.com/wazuh/wazuh/issues/10776), with around 40 comments. And I lived it myself in Phase 1. |
| **2. Tuning noisy rules without losing real detections** | 2026 threads with [hundreds of false positives](https://groups.google.com/g/wazuh/c/BiOLiJttJzA) or a [level 0 rule that failed to silence the alerts](https://groups.google.com/g/wazuh/c/zBEliuAWHDg). |
| **3. Knowing what Wazuh really detects** | People who [simulate attacks with Atomic Red Team](https://dev.to/jfash007/deploying-and-validating-a-wazuh-siem-with-atomic-red-team-26i4) and see that only some techniques raise an alert, and community tools built just to measure it, such as [`wazuhcoverage`](https://pypi.org/project/wazuhcoverage/0.4.0/). |
| **4. Reports and exports** | Requests for [full, scheduled reports](https://groups.google.com/g/wazuh/c/-bYUdw0mqAI) (2026), where the official answer was to [download them by hand](https://groups.google.com/g/wazuh/c/vNnC20JbbII). It includes my Phase 1 idea: gathering the failed SCA checks of all agents. |
| **5. Following up alerts** | Users looking for [a place to assign, investigate and close an alert](https://groups.google.com/g/wazuh/c/-fNsty6bIUY), and a [GitHub issue asking to send alerts to a case management tool](https://github.com/wazuh/wazuh/issues/33328). |
| **6. Asking Wazuh questions in natural language** | The community has already built MCP servers for this, like [this one, with around 210 GitHub stars](https://enterprisedna.co/directories/mcp/gbrigandi-mcp-server-wazuh). |

The threads show that the problem exists and repeats, but not how many people suffer it: many users never ask in public. So I also looked at thread views, comments on issues, and whether someone had to build their own tool to solve it.

## Which ones did you discard and why?

- **Noisy rules (2).** It is real, but to know whether a change loses detections you need a lot of real data from each user, and Mobius already reduces the noise when it triages. I keep it as a possible extension, because it uses the same pieces as the one I chose.
- **Knowing what Wazuh detects (3).** It needs attack simulations on the user's machines, and the community already has several tools. Also, it is often a consequence of need 1: the logs arrive, but nothing decodes them.
- **Reports (4).** Wazuh already has reports [on its roadmap](https://github.com/wazuh/wazuh-dashboard/issues/164) and the AI Analyst covers part of it in Wazuh Cloud. It is also hard for the AI to be the centre of a report. My Phase 1 idea still seems useful to me, but it is small: Wazuh already gives almost all the data.
- **Alert follow-up (5).** Mobius already covers the work on alerts, integrations with case management tools such as [TheHive](https://wazuh.com/blog/using-wazuh-and-thehive-for-threat-protection-and-incident-response/) already exist, and the AI wouldn't be the centre.
- **Natural language (6).** Wazuh 5.0 brings its own [AI assistant in the dashboard](https://github.com/wazuh/wazuh-documentation/releases/tag/v5.0.0-beta5), and community MCP servers already exist. It is a very crowded space.

## Which one did you choose, who does it benefit and how do they solve it today?

I chose need 1: **writing decoders and rules for logs that Wazuh doesn't understand.**

Wazuh only understands the logs it knows. If a company has a network device, an in-house application or an uncommon product, its logs reach Wazuh, but Wazuh can't read them and raises nothing. Teaching it takes two things: a decoder, which tells Wazuh how to read each line (where the user is, the IP...), and a rule, which tells it when to raise an alert and at what level.

I chose it for three reasons:

- **It has the most evidence**, and it still appears in 2026.
- **I lived it.** In Phase 1 I wrote my own decoder and my own rule, and it was the hardest part: the documentation is spread over several pages, the example rule had level 0 and didn't show up in the dashboard, and I ended up using AI to guide me, checking every step with `wazuh-logtest`.
- **It is going to grow.** Wazuh 5.0, which published its second release candidate on 30 October 2026, changes the format of decoders and rules: they move from XML to YAML, and rules follow the Sigma style. The [official transition plan](https://documentation.wazuh.com/5.0-beta/migration-to-5x/transition-plan.html) says not to copy 4.x files and to recreate custom rules and decoders with the new content system. There is no automatic converter: there are guides to translate them by hand, which [are not yet published on the documentation site](https://github.com/wazuh/wazuh-documentation/issues/10550). Also, a [test of the RC2](https://dev.to/xuxu298/wazuh-50-rc2-eight-things-that-still-change-for-your-4x-custom-rules-516i) showed that logs with no decoder are not even stored by default. If I put myself in the place of a company that uses Wazuh with its own content, it will have to rewrite it when it moves to 5.0, and every new source will still need its decoder. I think requests for help will increase.

**Who it benefits.** Any team that wants Wazuh to monitor something beyond what it ships with: network devices, in-house applications, uncommon security products or logs from SaaS services such as Google Workspace. Above all small teams, or a sysadmin without a dedicated detection engineer, which is the profile that asks the most on the mailing list.

**How they solve it today.**

- Reading several documentation pages and writing the decoder and the rule by hand.
- Testing and fixing with `wazuh-logtest` until it works.
- Asking on the mailing list and waiting for a Wazuh engineer to write it for them, which can take days.
- Copying decoders shared by other users.
- Asking a generic AI. In an [April 2026 thread](https://groups.google.com/g/wazuh/c/GMazaGqQbe4), a Wazuh engineer advises against it because the AI can produce wrong syntax and break the server.

**What exists and what is missing.** [Vesper](https://docs.vesper.wazuh.com/get-started/what-is-vesper) is the closest: it answers questions from community threads and can fix the deployment, but it doesn't build and test a decoder for each user's own log. Wazuh 5.0 brings a [space to validate and test custom content](https://documentation.wazuh.com/5.0-rc/user-manual/wazuh-indexer/modules/content-manager.html), but the user still writes it by hand. The tools exist; what is missing is someone who does the work for the user and checks that it works.

**I checked it in my lab.** With the Wazuh 4.14.9 API, a log from an invented application was not decoded and only a generic rule fired. I uploaded a decoder and a rule with `PUT /decoders/files` and `PUT /rules/files`, and `PUT /logtest` then returned the fields and the alert, without restarting the manager. A similar log that should not trigger the rule didn't trigger it, and a decoder with broken XML was rejected by the API with a clear error. The responses are in [`evidence/`](evidence/) and the details in [`research/RESEARCH.md`](research/RESEARCH.md#checked-in-my-lab).

**What it doesn't solve.** It doesn't collect the logs. If the logs don't reach Wazuh, as happens with Google Workspace without an integration, that is a different problem. And the engineer's warning is fair: the service only makes sense if the tests are good enough that nobody has to trust the AI blindly.

## Which service ideas did you consider before reaching yours and why did you drop them?

The previous questions are about problems. This one is about services: which product I thought of building before reaching the final one.

- **An AI assistant to ask your Wazuh questions.** It was my first idea, even before researching needs. At Prowler I used Lighthouse AI, an assistant you could ask anything and that guided you, and I missed it when using the Wazuh dashboard. I dropped it because Wazuh 5.0 already brings its own assistant in the dashboard, and there are also Mobius, Vesper and several community MCP servers. What I keep from this idea is the way of working: talking to the assistant and being guided by it.

Once I had chosen the need, I considered several ways to solve it:

- **A catalogue of decoders shared by the community.** It doesn't have the AI at the centre, and in Wazuh 5.0 the official content for the most common sources already arrives automatically from Wazuh CTI. What remains uncovered is the "long tail": your own application or an uncommon device.
- **An AI that writes the decoder and the rule, and nothing more.** It is exactly what the Wazuh engineer criticises: nobody checks that it works.
- **An AI that writes it and installs it by itself in the user's Wazuh.** Too risky: a mistake can stop it detecting attacks or even [stop the server](https://groups.google.com/g/wazuh/c/ZhOQTtJQj1c).

I settle on a middle ground: **an assistant the user talks to, that writes the decoder and the rule, tests them automatically before showing them and lets the person decide whether to install them.** The tests include logs that must raise an alert, logs that must not, and the typical mistakes I saw in the mailing list threads.

Migrating 4.x rules to the 5.0 format would be a natural extension, using the same tests. I don't put it at the centre because 5.0 is not yet the stable version and my lab is 4.x. The full design is in Phase 3.

## Sources

**What Wazuh already offers**
- Hub docs: https://hub.wazuh.com/docs
- What is Vesper: https://docs.vesper.wazuh.com/get-started/what-is-vesper
- Wazuh Cloud AI Analyst: https://documentation.wazuh.com/current/cloud-service/ai-analyst.html
- Wazuh 5.0, Content Manager: https://documentation.wazuh.com/5.0-rc/user-manual/wazuh-indexer/modules/content-manager.html
- Wazuh 5.0, documentation beta 5 release notes (dashboard AI Assistant): https://github.com/wazuh/wazuh-documentation/releases/tag/v5.0.0-beta5
- Wazuh 5.0, transition plan from 4.x: https://documentation.wazuh.com/5.0-beta/migration-to-5x/transition-plan.html
- Documentation issue about the 4.x to 5.x migration guides: https://github.com/wazuh/wazuh-documentation/issues/10550
- Test of Wazuh 5.0 RC2 with custom 4.x rules: https://dev.to/xuxu298/wazuh-50-rc2-eight-things-that-still-change-for-your-4x-custom-rules-516i

**Need 1: decoders and rules**
- Check Point (January 2026): https://groups.google.com/g/wazuh/c/jisvjBlUBdY
- OpenStack and the warning about AI (April 2026): https://groups.google.com/g/wazuh/c/GMazaGqQbe4
- FTP and Dovecot (April 2026): https://groups.google.com/g/wazuh/c/fPfi7dJoaeQ
- OLT fibre device (July 2026): https://groups.google.com/g/wazuh/c/1V8J7s-bx2k
- Sophos XGS firewall (August 2026): https://groups.google.com/g/wazuh/c/U66uWEwDOBw
- An invalid decoder file stops the manager: https://groups.google.com/g/wazuh/c/ZhOQTtJQj1c
- Google Workspace issue: https://github.com/wazuh/wazuh/issues/10776

**Need 2: noisy rules**
- Vulnerability false positives (January 2026): https://groups.google.com/g/wazuh/c/BiOLiJttJzA
- Level 0 rule that doesn't work (February 2026): https://groups.google.com/g/wazuh/c/zBEliuAWHDg

**Need 3: what Wazuh detects**
- Wazuh with Atomic Red Team: https://dev.to/jfash007/deploying-and-validating-a-wazuh-siem-with-atomic-red-team-26i4
- wazuhcoverage: https://pypi.org/project/wazuhcoverage/0.4.0/

**Need 4: reports**
- Full and scheduled reports (June 2026): https://groups.google.com/g/wazuh/c/-bYUdw0mqAI
- Scheduling reports (March 2026): https://groups.google.com/g/wazuh/c/vNnC20JbbII
- Reports roadmap issue: https://github.com/wazuh/wazuh-dashboard/issues/164

**Need 5: alert follow-up**
- Assigning, investigating and closing alerts (January 2026): https://groups.google.com/g/wazuh/c/-fNsty6bIUY
- Issue to send alerts to case management: https://github.com/wazuh/wazuh/issues/33328
- Wazuh and TheHive: https://wazuh.com/blog/using-wazuh-and-thehive-for-threat-protection-and-incident-response/

**Need 6: natural language**
- Community MCP server: https://enterprisedna.co/directories/mcp/gbrigandi-mcp-server-wazuh
