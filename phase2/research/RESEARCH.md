# Phase 2 research: finding a need

This file is the research behind `PHASE2.md`: where I looked, what I found, what I discarded and why, and a closer look at the need I chose. Each claim links to its source where it is made, and all the sources are listed again at the end, grouped by need.

I did this research with Claude (chat, with web search).

## 1. How I did the research

I worked in two rounds:

1. **Wednesday evening.** A first broad search for real needs, asking for 3 or 4 candidates with sources and the reasons to discard the rest.
2. **Thursday morning.** I repeated the search with more channels and gave priority to 2026 posts, so the needs are current and not things Wazuh solved years ago. This round also showed that Wazuh 5.0 is about to change how rules and decoders work, which changed how I see the problem.

Where I looked:

| Channel | Why |
|---|---|
| [Wazuh mailing list](https://groups.google.com/g/wazuh) (Google Groups) | Users describe their problems in their own words, and Wazuh engineers answer. It shows what people struggle with in practice. |
| GitHub issues ([`wazuh/wazuh`](https://github.com/wazuh/wazuh/issues), [`wazuh/wazuh-dashboard`](https://github.com/wazuh/wazuh-dashboard/issues)) | Feature requests, and how many people comment on them. |
| [Wazuh documentation](https://documentation.wazuh.com/current/) and blog | What Wazuh already does, so I don't propose something that exists. |
| [Wazuh Hub docs](https://hub.wazuh.com/docs) and Wazuh Cloud docs | What the Hub services and the AI Analyst already cover. |
| Community tools and practitioner articles | What people build themselves when Wazuh doesn't cover a need. |

Before looking for needs, I checked what already exists (section 2). A need that a Hub service or Wazuh itself already covers is not a good base for a new service.

## 2. What Wazuh already offers

### Wazuh Hub services

From the [Hub docs index](https://hub.wazuh.com/docs) and [Fleet](https://fleet.wazuh.com/wazuh/):

| Service | What it does |
|---|---|
| Mobius | Agentic SOC: an AI triages every alert, correlates escalations into incidents and can block an attacker IP ([what is Mobius](https://docs.mobius.wazuh.com/get-started/what-is-mobius)). |
| Vesper | AI support: answers Wazuh questions from resolved community threads, and its agent can inspect, diagnose and fix the user's deployment in read only, manual or auto mode ([what is Vesper](https://docs.vesper.wazuh.com/get-started/what-is-vesper)). |
| Pharos | Vulnerabilities and threat intelligence. |
| Argus | Security posture of cloud accounts (CSPM). |
| Umbra | Identity risk and dark web monitoring. |
| Lumen | Detection and response for generative AI use. |
| Fleet | All your Wazuh environments in one place. |

Mobius works on alerts that already exist. Vesper is the closest neighbour to the need I chose: it can answer "how do I write a decoder for X?" with threads where someone solved something similar. But it doesn't build the decoder for the user's own log and test it before handing it over.

### Wazuh Cloud AI Analyst

Only for Wazuh Cloud. It sends a periodic AI report on the security posture: alert activity, most active endpoints, vulnerabilities and MITRE techniques. Its parameters can't be customised, and it doesn't write or test rules ([AI Analyst docs](https://documentation.wazuh.com/current/cloud-service/ai-analyst.html), [Wazuh and AI for SOC workflows](https://thehackernews.com/2026/08/wazuh-and-ai-for-enhanced-soc-workflows.html)).

### Wazuh 5.0 (release candidate 2, published on 30 October 2026)

Wazuh 5.0 is not a normal upgrade, and it matters for this research. In August 2026 it was still in beta, and a Wazuh engineer recommended staying on 4.x for production, because in-place upgrades from 4.x are not supported ([mailing list](https://groups.google.com/g/wazuh/c/aD4iCWMN4Yc), [transitioning from 4.x to 5.x](https://documentation.wazuh.com/5.0-beta/migration-to-5x/index.html)). What changes:

- **New detection engine.** Decoders run on the manager and rules run on the indexer. Both are written in YAML, rules follow the Sigma style, and events are normalised to a common schema (WCS). Custom 4.x rules and decoders have to be rewritten.
- **Content Manager.** A place for the user's own decoders and rules, with three spaces: Draft, Test and Custom. Content is validated before it moves from one space to the next, it can be tested with logtest, and everything has a REST API ([Content Manager docs](https://documentation.wazuh.com/5.0-rc/user-manual/wazuh-indexer/modules/content-manager.html), [Ruleset Management in the dashboard](https://documentation.wazuh.com/5.0-rc/user-manual/wazuh-dashboard/wazuh-dashboard-configurations.html)).
- **AI Assistant in the dashboard.** You can ask about findings, vulnerabilities and agents in natural language.
- **Wazuh CTI.** Official rules and decoders are delivered and updated automatically.

So, with 5.0, asking questions about your data in natural language is covered by Wazuh itself. Writing your own decoders and rules is still manual, and it gets harder, because the format changes.

## 3. Needs I found

### N1. Writing decoders and rules for logs Wazuh doesn't understand

**What users say.** This is the most repeated problem I found. People send logs from a device or application that Wazuh has no decoder for, try to write one, and get stuck. It keeps happening in 2026:

- [Check Point](https://groups.google.com/g/wazuh/c/jisvjBlUBdY) (January): no decoder matched.
- [Trend Vision One](https://groups.google.com/g/wazuh/c/Uto2_1Im_0A) (February) and [Trend Micro Deep Security](https://groups.google.com/g/wazuh/c/X1iGLnEkziI) (April).
- [FTP and Dovecot](https://groups.google.com/g/wazuh/c/fPfi7dJoaeQ) (April): the default decoder keeps winning over the custom one.
- [OpenStack](https://groups.google.com/g/wazuh/c/GMazaGqQbe4) (April): no built-in decoders.
- [Barracuda email gateway](https://groups.google.com/g/wazuh/c/73G5SAZfymA/m/Hkwzvkh1BgAJ) (June).
- [An OLT fibre device](https://groups.google.com/g/wazuh/c/1V8J7s-bx2k) (July).
- [A Sophos XGS firewall](https://groups.google.com/g/wazuh/c/U66uWEwDOBw) (August).

And the same in earlier years: [OPNsense](https://groups.google.com/g/wazuh/c/iYBTHZLer5U), [Cisco ASA](https://groups.google.com/g/wazuh/c/jlNJiXIP-YM), [Cisco FMC and FTD](https://groups.google.com/g/wazuh/c/HTb5O64Wp6A), [BIND9](https://groups.google.com/g/wazuh/c/-lyCwbL5VQ0), [IIS](https://groups.google.com/g/wazuh/c/y06pKnpvn2o), [Netskope](https://groups.google.com/g/wazuh/c/mK4_VZSiWmg/m/pwyDCPBCCwAJ), [Barracuda ESG](https://groups.google.com/g/wazuh/c/tJU5xqN3QpU) and [Trend Micro Deep Security](https://groups.google.com/g/wazuh/c/DELoVYlBEqA).

The problems repeat: the decoder doesn't match ([learning to create decoders and rules](https://groups.google.com/g/wazuh/c/vOOhiQ2TGjM)), a default decoder wins over the custom one ([OPNsense filterlog](https://groups.google.com/g/wazuh/c/To50AoN_TII), [IIS](https://groups.google.com/g/wazuh/c/YGxAeMRK5KI)), it works in logtest but nothing shows up in the dashboard ([BIND9](https://groups.google.com/g/wazuh/c/-lyCwbL5VQ0)), or an invalid decoder file stops the manager ([API offline](https://groups.google.com/g/wazuh/c/ZhOQTtJQj1c)).

In many threads a Wazuh engineer ends up writing the decoder by hand. In the [Sophos thread](https://groups.google.com/g/wazuh/c/U66uWEwDOBw), the user shared his own decoders and rules, and the engineer found several mistakes (child decoders with different names, rule IDs outside the custom range, a custom user field that correlation can't use) and confirmed the fix with logtest.

There is also a [GitHub issue asking for Google Workspace decoders and rules](https://github.com/wazuh/wazuh/issues/10776). It is still open after years, with around 40 comments.

**How they handle it today.** Reading several documentation pages ([custom decoders](https://documentation.wazuh.com/current/user-manual/ruleset/decoders/custom.html), [custom rules](https://documentation.wazuh.com/current/user-manual/ruleset/rules/custom.html), [testing](https://documentation.wazuh.com/current/user-manual/ruleset/testing.html)), trial and error with `wazuh-logtest`, asking on the mailing list and waiting, or copying decoders shared by other users.

**My own experience.** I hit this in Phase 1. The documentation was split across three pages (decoder, rule, agent configuration), the example rule had level 0 and never showed up in the dashboard, and I used AI to guide me, checking every step with `wazuh-logtest`.

### N2. Too many alerts: tuning noisy rules without losing real detections

**What users say.** [Hundreds of false-positive vulnerability alerts](https://groups.google.com/g/wazuh/c/BiOLiJttJzA) from the same hosts (January 2026). A [level 0 rule to silence rootcheck alerts](https://groups.google.com/g/wazuh/c/zBEliuAWHDg), and the alerts came back the next day (February 2026). An [`overwrite` rule that didn't override anything](https://groups.google.com/g/wazuh/c/722TYuFVc8Q) (November 2025). [Overrides for one server that don't work](https://groups.google.com/g/wazuh/c/BppwPeQwecI) (2023). A [practitioner guide](https://www.decryptiondigest.com/blog/wazuh-tuning-false-positive-reduction-guide) recommends re-testing real attack samples with `wazuh-logtest` after every tuning change, which shows how manual it is.

**How they handle it today.** Writing level 0 or `overwrite` rules by hand, filters in the dashboard, or ignoring the alert channel ([triage cookbook](https://cookbook.solomonneas.dev/security/wazuh-triage/)). Mobius reduces the triage work, but the noisy rule keeps firing.

### N3. Knowing what Wazuh actually detects (coverage gaps)

**What users say.** People who [test Wazuh with Atomic Red Team](https://dev.to/jfash007/deploying-and-validating-a-wazuh-siem-with-atomic-red-team-26i4) see that logs arrive but only some techniques produce an alert. The community has built several tools just to measure this: a [detection harness](https://hawkinsoperations.pages.dev/case-study-detection-harness), [RedGap](https://pypi.org/project/redgap/) and [`wazuhcoverage`](https://pypi.org/project/wazuhcoverage/0.4.0/), which finds events in the archives that no decoder or rule handles.

**How they handle it today.** Manual attack simulations, reading archives, community scripts, and the MITRE dashboard, which shows what fired but not what is missing.

### N4. Reports and exports

**What users say.** [Full reports in PDF or CSV, exporting the whole dataset and scheduled reports by email](https://groups.google.com/g/wazuh/c/-bYUdw0mqAI) (June 2026). Asked [how to schedule reports](https://groups.google.com/g/wazuh/c/vNnC20JbbII), the answer in March 2026 was to download them by hand. An [aggregated CIS report for all agents](https://groups.google.com/g/wazuh/c/8S2aJig47Ys) has 580 views, and there are similar requests for [CIS reports by client](https://groups.google.com/g/wazuh/c/lw6oheeBCK0) and the [SCA results of all agents in one page](https://groups.google.com/g/wazuh/c/R0YNTobnpoA). This includes my Phase 1 idea: the failed SCA checks of all agents as one to-do list.

**How they handle it today.** Dashboard reports one by one, CSV exports, OpenSearch reporting, Grafana, or their own scripts.

### N5. Following up alerts (assign, close, keep a record)

**What users say.** A user [looks for a place to investigate alerts, assign them and mark them as closed](https://groups.google.com/g/wazuh/c/-fNsty6bIUY) (January 2026). A [GitHub issue asks for a button to send alerts to a case management tool](https://github.com/wazuh/wazuh/issues/33328).

**How they handle it today.** Integrations with case management tools such as [TheHive](https://wazuh.com/blog/using-wazuh-and-thehive-for-threat-protection-and-incident-response/) or DFIR IRIS.

### N6. Asking Wazuh questions in natural language

**What users say.** Users want to ask "what happened on this server?" instead of building queries. The community already built MCP servers for this: [one in Rust with around 210 GitHub stars](https://enterprisedna.co/directories/mcp/gbrigandi-mcp-server-wazuh) and [another with 48 tools](https://www.remoteopenclaw.com/mcp/gensecaihq/Wazuh-MCP-Server).

**How they handle it today.** Those community MCP servers, dashboards and manual queries. Wazuh 5.0 adds its own [AI Assistant in the dashboard](https://github.com/wazuh/wazuh-documentation/releases/tag/v5.0.0-beta5).

Why I discarded N2 to N6 is in `PHASE2.md`.

## 4. Comparison of the main candidates

| | Recent evidence | Already covered | AI at the core | My experience |
|---|---|---|---|---|
| N1. Decoders and rules | High | No | Yes, with tests | Phase 1 |
| N2. Noisy rules | Medium | Partly (Mobius) | Yes | No |
| N3. Coverage | Medium | Community tools | Partly | No |
| N4. Reports | High | Roadmap, AI Analyst | Little | Phase 1 (SCA) |
| N5. Alert follow-up | Medium | Mobius, 5.0 | No | No |
| N6. Natural language | Medium | 5.0 AI Assistant, MCP | Yes | Prowler |

I chose **N1**. The reasons are in `PHASE2.md`.

## 5. N1 in depth

### Who it affects

Any team that wants Wazuh to monitor something outside the built-in ruleset: network devices, in-house applications, niche security products or SaaS logs. Usually it is a small team or a sysadmin, without a dedicated detection engineer.

### What exists today and what is missing

| What exists | What it does | What is missing |
|---|---|---|
| Documentation and `wazuh-logtest`, also through the API with [`PUT /logtest`](https://documentation.wazuh.com/current/user-manual/ruleset/testing.html) | Gives you the pieces and lets you test a log | You write everything by hand |
| [Content Manager](https://documentation.wazuh.com/5.0-rc/user-manual/wazuh-indexer/modules/content-manager.html) (5.0) | Organises, validates and tests your content | It doesn't write anything for you |
| Wazuh CTI (5.0) | Official content for popular sources | It doesn't cover the long tail: your own app, a rare device |
| Wazuh engineers on the mailing list | Write the decoder for you | It takes days and depends on someone answering |
| [Vesper](https://docs.vesper.wazuh.com/get-started/what-is-vesper) | Answers from similar community threads and fixes the deployment | It doesn't build and test a decoder for your own log |
| A general AI chat | Gives you an XML or YAML | Nobody tests it |
| Mobius, AI Assistant | Triage and questions | They don't create detection content |

The tools exist, but nobody does the work for the user and checks that it works.

### The concern about AI

In the [OpenStack thread](https://groups.google.com/g/wazuh/c/GMazaGqQbe4) (April 2026), a Wazuh engineer advises against using AI tools to create decoders and rules, because they can produce wrong syntax and break the server if uploaded directly. Another thread shows that [an invalid decoder file can stop the manager](https://groups.google.com/g/wazuh/c/ZhOQTtJQj1c). This is a fair point, and it is the main risk of this need. The [Sophos thread](https://groups.google.com/g/wazuh/c/U66uWEwDOBw) shows the same thing with a human: the decoders looked fine but had several mistakes that only testing revealed.

So the answer can't be "AI writes it and you trust it". The output has to be tested automatically before anyone sees it: syntax, logs that must match, logs that must not match, and the known mistakes above. A person always decides whether to install it. I develop this in `PHASE2.md` and in Phase 3.

### Wazuh 5.0 makes the need bigger

- Custom 4.x rules and decoders must be recreated to move to 5.0. The [official transition plan](https://documentation.wazuh.com/5.0-beta/migration-to-5x/transition-plan.html) says not to copy 4.x configuration files and to recreate custom rules and decoders with the new content management system. There is no automatic converter: the migration guides explain how to translate them by hand, and a [documentation issue](https://github.com/wazuh/wazuh-documentation/issues/10550) notes that these guides are not even published on the documentation site yet. A [practitioner article](https://dev.to/xuxu298/wazuh-50-will-not-load-your-custom-xml-rules-82-of-one-public-pack-depends-on-what-it-drops-1h01) counted that most rules in one public community pack use features the 5.0 guide marks as unsupported.
- A user testing a 5.0 beta said that the [new way of creating rules is a big change compared with how simple it was before](https://groups.google.com/g/wazuh/c/Grq6Hmpfea0).
- Users who stay on 4.x don't need to change anything for now: 5.0 is still a release candidate, and in-place upgrades from 4.x are not supported.
- A [practitioner who tested 5.0 RC2](https://dev.to/xuxu298/wazuh-50-rc2-eight-things-that-still-change-for-your-4x-custom-rules-516i) found that 4.x XML files load without errors but show no sign of running, and that log lines with no decoder are not indexed by default. In his lab, the lines from his home-made app simply disappeared.
- In 5.0, official rules and decoders are published through Wazuh CTI, and a community member [asked how to propose new content](https://groups.google.com/g/wazuh/c/-9ThP4C-o3Y), since the old GitHub process no longer applies.

Every Wazuh user with custom content will have to rewrite it when they move to 5.0, and logs that nothing decodes become invisible.

### An example: Google Workspace

Wazuh has no official Google Workspace integration ([GitHub issue #10776](https://github.com/wazuh/wazuh/issues/10776)). The usual workaround is to send the audit logs to Google Cloud, read them with the GCP integration, and then write your own rules. Two mailing list threads on this have more than 2,000 views each ([Gsuite integration](https://groups.google.com/g/wazuh/c/mYhwsL5L2Vo), [Google Workspace logs integration](https://groups.google.com/g/wazuh/c/fqmc8OpNtIk)). Some users built their own collector ([Google Workspace logs to Wazuh update](https://groups.google.com/g/wazuh/c/5mzmOEkDQzQ)).

I worked with Google Workspace at Prowler, so I know what its audit logs contain. Collecting the logs is a separate problem, so I use Google Workspace as an example of the need, not as the scope of the service.

### Checked in my lab

- In Phase 1 I wrote a decoder and a rule for my own log file and tested them with `wazuh-logtest` before seeing the alert in the dashboard.
- I then repeated the cycle through the Wazuh 4.14.9 REST API, with an invented application called `miapp`. The responses are saved in `../evidence/`:
  - `PUT /logtest` with a `miapp` log: no decoder, no fields, only the generic rule 1002 (level 2), `alert: false` (`api-logtest-unknown.json`).
  - `PUT /decoders/files/miapp_decoder.xml` and `PUT /rules/files/miapp_rules.xml` uploaded my decoder and rule, and `GET /manager/configuration/validation` stayed `OK`.
  - The same log in a new `PUT /logtest` session: decoder `miapp`, fields `srcuser`, `srcip` and `reason`, rule 100100 (level 5), `alert: true` (`api-logtest-after.json`). No manager restart was needed.
  - A similar log with `reason=ok` decoded the same way but did not fire rule 100100 (`api-logtest-negative.json`).
  - A decoder with broken XML was rejected with error 1113, "XML syntax error", and was not saved (`api-put-decoder-broken.json`).
  - I deleted the test files afterwards with `DELETE /decoders/files/...` and `DELETE /rules/files/...`.

## 6. Sources

### Wazuh and Hub offering
- Wazuh Hub docs: https://hub.wazuh.com/docs
- Fleet: https://fleet.wazuh.com/wazuh/
- What is Mobius: https://docs.mobius.wazuh.com/get-started/what-is-mobius
- What is Vesper: https://docs.vesper.wazuh.com/get-started/what-is-vesper
- Wazuh Cloud AI Analyst: https://documentation.wazuh.com/current/cloud-service/ai-analyst.html
- Wazuh and AI for SOC workflows (August 2026): https://thehackernews.com/2026/08/wazuh-and-ai-for-enhanced-soc-workflows.html
- Wazuh 5.0 Content Manager: https://documentation.wazuh.com/5.0-rc/user-manual/wazuh-indexer/modules/content-manager.html
- Wazuh 5.0 dashboard, Ruleset Management: https://documentation.wazuh.com/5.0-rc/user-manual/wazuh-dashboard/wazuh-dashboard-configurations.html
- Wazuh 5.0 documentation beta 5 release notes (AI Assistant in the dashboard): https://github.com/wazuh/wazuh-documentation/releases/tag/v5.0.0-beta5
- Transitioning from 4.x to 5.x: https://documentation.wazuh.com/5.0-beta/migration-to-5x/index.html
- Transition plan, recreate custom rules and decoders: https://documentation.wazuh.com/5.0-beta/migration-to-5x/transition-plan.html
- Documentation issue, 4.x to 5.x migration guides not published: https://github.com/wazuh/wazuh-documentation/issues/10550
- Wazuh 5.0 will not load custom XML rules (practitioner article): https://dev.to/xuxu298/wazuh-50-will-not-load-your-custom-xml-rules-82-of-one-public-pack-depends-on-what-it-drops-1h01
- Wazuh 5 still in beta, 4.x recommended for production (August 2026): https://groups.google.com/g/wazuh/c/aD4iCWMN4Yc

### N1. Decoders and rules
- Check Point decoder (January 2026): https://groups.google.com/g/wazuh/c/jisvjBlUBdY
- Trend Vision One decoders (February 2026): https://groups.google.com/g/wazuh/c/Uto2_1Im_0A
- Trend Micro Deep Security (April 2026): https://groups.google.com/g/wazuh/c/X1iGLnEkziI
- FTP and Dovecot, default decoder wins (April 2026): https://groups.google.com/g/wazuh/c/fPfi7dJoaeQ
- OpenStack, engineer advises against AI-generated decoders (April 2026): https://groups.google.com/g/wazuh/c/GMazaGqQbe4
- Barracuda email gateway (June 2026): https://groups.google.com/g/wazuh/c/73G5SAZfymA/m/Hkwzvkh1BgAJ
- OLT fibre device decoder (July 2026): https://groups.google.com/g/wazuh/c/1V8J7s-bx2k
- Sophos XGS decoders and rules reviewed by a Wazuh engineer (August 2026): https://groups.google.com/g/wazuh/c/U66uWEwDOBw
- IIS decoders (November 2025): https://groups.google.com/g/wazuh/c/y06pKnpvn2o
- Disable IIS default decoders (January 2026): https://groups.google.com/g/wazuh/c/YGxAeMRK5KI
- OPNsense filterlog, default decoder wins (December 2025): https://groups.google.com/g/wazuh/c/To50AoN_TII
- Invalid decoder file stops the manager (September 2025): https://groups.google.com/g/wazuh/c/ZhOQTtJQj1c
- OPNsense decoder: https://groups.google.com/g/wazuh/c/iYBTHZLer5U
- Cisco ASA decoder: https://groups.google.com/g/wazuh/c/jlNJiXIP-YM
- Cisco FMC and FTD: https://groups.google.com/g/wazuh/c/HTb5O64Wp6A
- BIND9 decoder, works in tests but not in Discover: https://groups.google.com/g/wazuh/c/-lyCwbL5VQ0
- Netskope CEF decoder: https://groups.google.com/g/wazuh/c/mK4_VZSiWmg/m/pwyDCPBCCwAJ
- Barracuda ESG decoders: https://groups.google.com/g/wazuh/c/tJU5xqN3QpU
- Trend Micro Deep Security decoder: https://groups.google.com/g/wazuh/c/DELoVYlBEqA
- Learning to create decoders and rules: https://groups.google.com/g/wazuh/c/vOOhiQ2TGjM
- GitHub issue, Google Workspace rules and decoders: https://github.com/wazuh/wazuh/issues/10776
- Gsuite integration: https://groups.google.com/g/wazuh/c/mYhwsL5L2Vo
- Google Workspace logs integration: https://groups.google.com/g/wazuh/c/fqmc8OpNtIk
- Google Workspace logs to Wazuh update: https://groups.google.com/g/wazuh/c/5mzmOEkDQzQ
- Wazuh 5.0 beta, new way of creating rules (June 2026): https://groups.google.com/g/wazuh/c/Grq6Hmpfea0
- Contributing rules and decoders in 5.0 (September 2026): https://groups.google.com/g/wazuh/c/-9ThP4C-o3Y
- Wazuh 5.0 RC2 and 4.x custom rules (practitioner test): https://dev.to/xuxu298/wazuh-50-rc2-eight-things-that-still-change-for-your-4x-custom-rules-516i
- Wazuh docs, custom decoders: https://documentation.wazuh.com/current/user-manual/ruleset/decoders/custom.html
- Wazuh docs, custom rules: https://documentation.wazuh.com/current/user-manual/ruleset/rules/custom.html
- Wazuh docs, testing decoders and rules (`wazuh-logtest` and `PUT /logtest`): https://documentation.wazuh.com/current/user-manual/ruleset/testing.html
- Wazuh blog, creating decoders and rules from scratch: https://wazuh.com/blog/creating-decoders-and-rules-from-scratch/

### N2. Noisy rules
- Vulnerability scanner false positives (January 2026): https://groups.google.com/g/wazuh/c/BiOLiJttJzA
- Rootcheck alerts in LXC, level 0 rule not working (February 2026): https://groups.google.com/g/wazuh/c/zBEliuAWHDg
- False positive rule not working (November 2025): https://groups.google.com/g/wazuh/c/722TYuFVc8Q
- Tuning Wazuh rules: https://groups.google.com/g/wazuh/c/BppwPeQwecI
- Practitioner tuning guide: https://www.decryptiondigest.com/blog/wazuh-tuning-false-positive-reduction-guide
- Wazuh triage cookbook: https://cookbook.solomonneas.dev/security/wazuh-triage/

### N3. Coverage
- Wazuh with Atomic Red Team: https://dev.to/jfash007/deploying-and-validating-a-wazuh-siem-with-atomic-red-team-26i4
- Wazuh detection harness: https://hawkinsoperations.pages.dev/case-study-detection-harness
- RedGap: https://pypi.org/project/redgap/
- wazuhcoverage: https://pypi.org/project/wazuhcoverage/0.4.0/

### N4. Reports
- Reporting solutions for Wazuh (June 2026): https://groups.google.com/g/wazuh/c/-bYUdw0mqAI
- Scheduling reports (March 2026): https://groups.google.com/g/wazuh/c/vNnC20JbbII
- Aggregated CIS report of all agents: https://groups.google.com/g/wazuh/c/8S2aJig47Ys
- CIS reports by client: https://groups.google.com/g/wazuh/c/lw6oheeBCK0
- SCA report for all agents: https://groups.google.com/g/wazuh/c/R0YNTobnpoA
- Dashboard reporting roadmap issue: https://github.com/wazuh/wazuh-dashboard/issues/164

### N5. Alert follow-up
- Alerts tab to investigate, assign and close (January 2026): https://groups.google.com/g/wazuh/c/-fNsty6bIUY
- GitHub issue, send alerts to case management: https://github.com/wazuh/wazuh/issues/33328
- Wazuh and TheHive: https://wazuh.com/blog/using-wazuh-and-thehive-for-threat-protection-and-incident-response/

### N6. Natural language
- Community MCP server (Rust): https://enterprisedna.co/directories/mcp/gbrigandi-mcp-server-wazuh
- Community MCP server (48 tools): https://www.remoteopenclaw.com/mcp/gensecaihq/Wazuh-MCP-Server
