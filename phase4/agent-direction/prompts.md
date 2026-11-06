# Prompts

The prompts that did the most to direct the AI in phases 2 and 3.

## Phase 2: research

Tool: Claude (chat, with web search). The prompts are in the order I used them. I wrote them in Spanish; this is a translation.

### 1. First search for needs (Wednesday evening)

Run a deep research to find real needs that Wazuh users have expressed. First, check what the Wazuh Hub services already offer, so that we do not propose something that exists. For each need, document where it comes from, with links. Also list the needs you discard and why: because they are not worth pursuing, because they cannot be done, or for any other reason. Give me 3 or 4 real needs, and then we will choose one together.

### 2. Second round, with more channels (Thursday morning)

Repeat the research from scratch. Look at the open feature requests in the Wazuh GitHub repositories and how many people comment on them, the mailing list, the documentation and the community tools. Give priority to posts from 2026, so that the needs are current. Add any other source you find useful. Then give me the list of needs again, with sources, so that we can go through them step by step.

### 3. Choosing between the candidates

Compare the needs by how much recent evidence there is for each, whether something already covers it, whether AI can be the core of the service, and whether I have experience with it. Which one would you choose, and why?

My first idea was an AI assistant to ask questions about your own Wazuh, like Lighthouse AI in Prowler. What do you think of going in that direction?

### 4. Going deeper into the chosen need

I will go with the need about writing decoders and rules for logs that Wazuh does not understand. Research it further. Should the service focus on Google Workspace or be general? Explain clearly what the service would do and how it differs from what already exists. A Wazuh engineer advises against using AI to write decoders: how would the service address that?

### 5. Checking the sources

Whenever a claim comes from an issue or a thread, link it right there, in addition to the list at the end. Check that every link works.

## Phase 3: lab checks

Tool: Claude Code on my Mac. I gave all the checks in one prompt; here it is split by block. I wrote it in Spanish; this is a translation.

### 1. Context and rules

Let's run the Phase 3 lab checks on my Mac, where my Wazuh 4.14.9 runs in Docker.

Rules: never print passwords or full tokens. If you need my API password, ask me to type it without showing it, and do not write it to any file. Save every useful result in the Phase 3 evidence folder, with clear names and no secrets. Before each block, tell me in one sentence what you are going to do, and wait for my approval before touching my real Wazuh. Do not commit.

### 2. Cleanup left over from Phase 2

Check that only my original rules file is left on my manager, and that the test rule from Phase 2 was deleted. Save the answer as evidence.

### 3. A sandbox with only the manager

Start a new container from the official Wazuh manager image, isolated and with no network. Do not touch my own containers. Measure how long it takes until the analysis engine is running and logtest answers, and how much memory it uses.

Copy my Phase 2 decoder and rule into it and, without restarting, test one of my `miapp` login logs with logtest. My decoder and my rule must appear. Save the startup time, the memory, the trimmed logtest output and confirmation that the container had no network.

### 4. Wazuh's test bank detects a rule that silences alerts

In the same sandbox, run the official ruleset test bank of my Wazuh version and write down how many cases pass.

Then add a level 0 rule that silences the SSH alerts for login attempts with a non-existent user. Run the test bank again and write down which cases fail now. Also test an SSH log of that kind with logtest, before and after adding the rule.

Save the results before and after, the failing cases and the logtest output. If the test bank does not work inside the container after two reasonable attempts, stop, tell me why, and do at least the logtest part. When you finish, delete the container.

### 5. An API user with minimum permissions, on my real Wazuh (wait for my approval)

Create an API user with a random password that is never shown or saved. Give it a role with only these permissions: read the manager information, read decoders, read rules and run logtest.

With that user, check that it can read the manager information, list my rules and test the SSH log, and that it gets a permissions error when it tries to write a rule. Save each call with its HTTP code and message, without tokens. Then delete the user, the role and the policy, and check that they no longer exist.

### 6. Diagrams and summary

Check that the 6 Phase 3 diagrams render without errors. If any fails, tell me the error and propose the fix, without changing what it says. At the end, give me a short summary of each block with the numbers you got.
