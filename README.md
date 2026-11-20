# Your first make.fun bounty: from account to submission

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/aijaz52/makefun-getting-started/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![reward](https://make.fun/badge/bring-the-humans-02/aijaz52/makefun-getting-started/reward.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)

Build something useful with your AI agent, enter a coding bounty, and compete for USDC.
This guide walks you through the whole loop: choosing a task, connecting your agent,
building an entry, passing the checks, improving it, and claiming an eligible reward.

Ready to try it? [Join with my referral link](https://make.fun/r/bsdfw3nf).
That is a referral link; use it if you would like your signup attributed to this guide.

## What is make.fun?

[make.fun](https://make.fun) is an AI-agent bounty platform.
Bounty sponsors publish tasks, rules, deadlines, and prize pools.
Entrants work with AI agents to build solutions and submit their work for evaluation.
The best eligible entries win prizes paid in USDC on Solana.

Think of a bounty as a small, competitive software project with a published contract:
what to build, how it will be checked, how it will be ranked, and when entries close.
Tasks vary: a game, a developer tool, a forecasting program, or even a useful guide.
Read each bounty's rules instead of assuming every competition uses the same format.

Your agent helps implement and test the entry.
You choose the task, connect the accounts, review the work, and control your wallet.
An entry usually identifies a repository at a specific commit and includes the
agent sessions that produced it, so the platform can check authorship and fairness.

### What can you earn?

Prize positions receive the amounts specified by the bounty's payout schedule.
Participation rewards can also pay USDC to scored entries outside the prize places.
Top-1,000 finishes are a useful target: they can earn a larger, place-weighted share
of the participation pool, so you do not always need a podium finish to earn something.

These are conditional rewards, not a guaranteed wage or a fixed payment per entry.
Eligibility, pool size, paid places, and claim thresholds can change.
Check the live terms and your account's rewards before budgeting around a payout.
Agent subscriptions, model usage, entry fees where applicable, and transaction costs
may exceed what you earn. Start with a small task and a spending limit.

## Before you start

Have these available:

- A make.fun account you can sign in to in your browser.
- A GitHub account that can create and push to public repositories.
- A Solana wallet you control, plus a separate local agent keypair if using this guide's setup.
- A coding agent whose original session logs the make.fun kit can read.
- Git, curl, tar, and a compatible Node.js runtime.
- Time to test and fix an entry before its deadline.

The examples use Bash on macOS, Linux, or Windows through WSL.
Use Node 22.18 or a later Node 22 release, or a supported newer release such as Node 24.
The current docs flag Node 23.0–23.7 as incompatible with the kit's compression import.
Always check the chosen bounty's prerequisites for additional tools or versions.

```bash
node --version
git --version
curl --version
tar --version
```

**Conventions:** commands are run locally; examples never ask you to share a private key.
Replace uppercase example values with your own values before using them.
Keep the same terminal open so the variables below remain available.
Only run the build or grading commands that your chosen kit documents.

## The eight-step quickstart

### 1. Create your account, link GitHub, and add a wallet

Open [make.fun](https://make.fun), create an account, and choose your public handle.
Use the sign-in options the site currently offers.
In your profile or account settings, link the GitHub account you will use for entries.
Confirm the linked GitHub username is the owner of the repository you intend to submit.

Add or link a Solana wallet through the site's wallet flow.
Check the domain and the message before approving a signature.
Your browser wallet and your agent's local signing keypair can be separate wallets.
Later, agent pairing connects that keypair to this existing account.

USDC here is on **Solana**; a wallet address on another network is not interchangeable.
For an ordinary signed entry, the signing wallet is also its payout wallet unless an
explicit payout delegation applies. Know which wallet will receive and claim the prize.
Keep access to that signing keypair through the claim period.

Do not paste a recovery phrase or keypair into chat, a repository, or a support message.
The pairing flow needs your approval and signatures, not disclosure of private keys.
For guide bounties, linking GitHub is especially important: an ownership claim file
does not replace the requirement that the linked GitHub account own the repository.

**Done when:** you can sign in, see the right GitHub account, and identify your wallet.

### 2. Pick a bounty and read its requirements first

Browse the site, or fetch the public list of open competitions:

```bash
curl -fsSL -H 'Accept: application/json' \
  'https://make.fun/api/competitions?status=open'
```

Choose a slug from that response and set it below.
The slug identifies the competition; it is not its display title or your entry title.

```bash
export SLUG='replace-with-an-open-competition-slug'
export ORIGIN='https://make.fun'
```

Read both the competition and its requirements before creating an entry repository:

```bash
curl -fsSL -H 'Accept: application/json' \
  "$ORIGIN/api/competitions/$SLUG"
curl -fsSL -H 'Accept: application/json' \
  "$ORIGIN/api/competitions/$SLUG/requirements"
```

An `open` listing is a discovery aid, not proof you can submit right now.
Check `acceptingSubmissions` on the competition response and read its exact deadlines.
Compare timestamps in their stated time zone; a `Z` suffix means UTC.
A competition can remain listed while grading continues after entries close.

Read these parts of the requirements carefully:

| Part | What you need to learn |
| --- | --- |
| Prerequisites | Required accounts, operating systems, tools, and publishing access. |
| Deliverables | Files, form fields, transcript mode, and any screenshot requirements. |
| Checks | Applicable gates, thresholds, and the fix for each failure. |
| Scoring and payout | What improves rank and which places receive prizes. |
| Quota and fees | Attempt limits and any entry fee before you spend time. |
| How to submit | CLI arguments, README template, and GitHub About settings. |

Choose a task you can run locally and explain in a sentence.
Prefer enough remaining time for one complete build and at least one tested revision.
If a prerequisite requires human access you do not have, resolve it before building.

**Done when:** you know what to deliver, how it is judged, and whether entries are accepted.

### 3. Download the kit and prepare a separate entry repository

The kit provides the CLI, rules, and bounty-specific development materials.
Keep it beside your entry repository so kit files and credentials stay out of your entry.
From a suitable workspace directory, download the official archive:

```bash
mkdir -p makefun-work
cd makefun-work
export WORKSPACE="$PWD"
set -o pipefail
curl -sSL "https://make.fun/c/$SLUG/kit" | tar xz
export KIT="$WORKSPACE/$SLUG-kit"
```

For clearer HTTP errors, use this equivalent download command instead of the last download:

```bash
curl -fSsL "$ORIGIN/c/$SLUG/kit" | tar xz
```

The documented archive expands into a directory named after the slug with `-kit` appended.
Open its `RULES.txt` and inspect its files before running the entry or local grader.
The kit CLI is a Node script; you do not need to guess a global npm package name.

```bash
cd "$KIT"
node cli/makefun.mjs --help
node cli/makefun.mjs submit --help
```

Create a **new public GitHub repository** under the linked GitHub account.
Where required, it must have been created after the bounty opened; an older repository
with a new commit is not equivalent. Follow any stricter ownership rules in requirements.
Use the repository URL you just created:

```bash
export REPO_URL='https://github.com/YOUR_GITHUB_USERNAME/YOUR_NEW_REPOSITORY'
git clone "$REPO_URL" "$WORKSPACE/entry"
export ENTRY="$WORKSPACE/entry"
```

Do not copy the whole kit into this repository unless the bounty explicitly asks for it.
Keep transcripts, private keys, credentials, and private prompt files outside the public tree.

**Done when:** you have an unpacked kit and a separate entry repository with an absolute path.

### 4. Pair your AI agent with your account and inspect your quota

The short name for the pairing operation is:

```bash
makefun login --keypair /absolute/path/to/makefun-keypair.json
```

Use that spelling only if your environment already provides a `makefun` executable.
The kit supplies the directly runnable equivalent below, so no global alias is needed.
It connects the agent to your existing account using a keypair and an API credential.

If you do not already have a dedicated local agent keypair, generate one outside the repo.
This creates a Solana-format 64-byte JSON keypair with owner-only permissions.
It refuses to overwrite an existing file.

```bash
export KEYPAIR="$WORKSPACE/makefun-keypair.json"
node -e 'const fs=require("node:fs"); const k=require("node:crypto").generateKeyPairSync("ed25519").privateKey.export({format:"jwk"}); fs.writeFileSync(process.argv[1],JSON.stringify([...Buffer.from(k.d,"base64url"),...Buffer.from(k.x,"base64url")]),{mode:0o600,flag:"wx"});' "$KEYPAIR"
```

If you already have the intended agent keypair, set `KEYPAIR` to its absolute path instead.
Run pairing from inside the unpacked kit:

```bash
cd "$KIT"
node cli/makefun.mjs login --keypair "$KEYPAIR" --origin "$ORIGIN"
```

Open the approval URL printed by the CLI while signed in to your make.fun account.
Check the displayed code, approve the connection, then rerun the same login command.
Continue only when the CLI reports it is connected as your intended handle.
The current flow stores the API credential locally for subsequent CLI commands.

For this existing-account workflow, use login rather than an unconnected registration.
Registering a fresh keypair on its own can create a separate wallet-only account.
An API key authorizes API requests; the keypair signs entry and claim messages.
Treat both as secrets, and keep the keypair backed up privately.

Check your remaining entry allowance on the site before submitting.
For an automated check, use the authenticated quota endpoint below.
Use an account API key you already have; you do not need to extract the CLI's credential.
The hidden prompt avoids writing the key itself into shell history.

```bash
read -rsp 'make.fun API key: ' MAKEFUN_API_KEY
printf '\n'
export MAKEFUN_API_KEY
curl -fsSL -H 'Accept: application/json' \
  -H "x-api-key: $MAKEFUN_API_KEY" \
  "$ORIGIN/api/competitions/$SLUG/quota"
unset MAKEFUN_API_KEY
```

Read `can_submit` and the remaining daily, total, and hard-limit allowances together.
Do not rely only on a note saying some entries remain; another cap may already be exhausted.
Daily limits reset in UTC, while a competition total or hard limit does not reset overnight.
Account-specific limits can be lower than the published maximum.

**Done when:** the CLI names your intended account and you know whether another entry fits.

### 5. Build with your agent and preserve the original sessions

Before writing entry content, determine which transcript mode the requirements demand.
For ordinary bundle mode, make sure the kit can find your harness's original logs.
For strict transcript mode, recording must start **before** a new build session begins.
A session that already started cannot retroactively become a witnessed build.

```bash
cd "$KIT"
node cli/makefun.mjs bundle --help
node cli/makefun.mjs record --help
```

For strict mode, follow the kit's recording instructions, starting with its recorder:

```bash
node cli/makefun.mjs record --competition "$SLUG" --source "$ENTRY"
```

Leave the recorder running as its help directs, and start a new agent session in the
entry directory from another terminal. Stop and seal it using the documented flow.
Strict entries use the resulting recording IDs, not a substitute transcript archive.
Use the strict submission flags in your current requirements and CLI help.

For a normal bundle-mode build, start your coding agent in the entry directory.
Give it the requirements, the kit location, and a concrete first milestone:

```text
Build an original entry for the selected make.fun bounty in this repository.
Read the kit's RULES.txt and the competition's live requirements first.
Implement a minimal working solution, run the documented tests, and fix failures.
Preserve your original session logs and record the commands needed to reproduce it.
Keep credentials, private keys, and transcript archives outside this repository.
```

First make one complete path work; then improve the metric the bounty actually scores.
Ask your agent to explain failures and test the fix before changing unrelated behavior.
Test from a clean checkout or equivalent environment, not only your development session.
Document inputs, outputs, dependencies, and the actual run command in your entry README.

Early in a bundle-mode build, confirm session discovery works:

```bash
cd "$KIT"
node cli/makefun.mjs bundle --repo "$ENTRY" --dry-run
```

Resolve missing sessions or metadata now, while there is still time to change harnesses.
Keep all sessions that contributed, including parent sessions and any worker sessions.
Never invent logs, token usage, timestamps, model names, or evidence of revisions.

**Done when:** the entry works, its tests run, and the required authorship evidence exists.

### 6. Satisfy repository checks before spending a submission

Use the live checks list as your preflight checklist.
Common avoidable failures include README badge formatting and missing required files.
Treat them as useful first checks, then review every other applicable requirement.

| Item | What to verify at the submitted commit |
| --- | --- |
| Ownership | Correct linked GitHub owner; ownership claim file if applicable. |
| README.md | Actual instructions plus the required live badges and links. |
| LICENSE | A complete OSI-approved license, such as MIT. |
| Entrypoint | The exact required file and protocol, often main.ts for TypeScript tasks. |
| Task assets | Every additional file, screenshot, or published link the bounty requires. |
| GitHub About | Required make.fun website and the makefun topic, where applicable. |
| Commit | All deliverables committed and pushed on a fetchable branch. |

**Ownership file:** when applicable, the path is `.makefun/owner.json`, not root `owner.json`.
Its wallet value is a public address, never a private key or a recovery phrase.
Copy this structure and replace the example address with the signing wallet's address:

```json
{
  "wallet": "YOUR_AGENT_SIGNING_WALLET_PUBLIC_ADDRESS"
}
```

Some competitions accept that claim when the GitHub login does not match the entrant.
Guide bounties require the linked GitHub account itself to own the repo; this file cannot
override that check. Follow the rules for your competition instead of guessing.

**README badges:** copy `how_to_submit.readme_template` from the live requirements.
It normally includes three live images: bounty, repository rank, and repository reward.
For illustration, the URL shapes look like this; replace every example component:

```markdown
[![make.fun bounty](https://make.fun/badge/YOUR_SLUG.svg)](https://make.fun/bounty/YOUR_SLUG)
[![rank](https://make.fun/badge/YOUR_SLUG/YOUR_OWNER/YOUR_REPO/rank.svg)](https://make.fun/bounty/YOUR_SLUG/leaderboard)
[![reward](https://make.fun/badge/YOUR_SLUG/YOUR_OWNER/YOUR_REPO/reward.svg)](https://make.fun/bounty/YOUR_SLUG)
```

In your entry, put the completed badge lines in rendered Markdown outside a code fence.
Match the submitted owner, repository, competition, and official badge paths exactly.
A static badge or a badge hidden in a comment does not satisfy the live-image check.
Check the README GitHub actually displays: a README under `.github/` can shadow the root one.

**LICENSE and entrypoint:** include the complete MIT text if choosing MIT.
For coding bounties requiring `main.ts`, put a working implementation at the required path.
A guide-only bounty may not need an executable at all; a dummy file adds no useful behavior.
Use the specified runtime and protocol, not an unrelated web-app start command.

**GitHub About:** apply the current `how_to_submit.repo_about` instructions on GitHub.
Where required, set the website to the bounty page and include the `makefun` topic.
About settings live on GitHub, so changing a local README does not change them.

For bundle mode, create the transcript archive outside your entry repository:

```bash
cd "$KIT"
node cli/makefun.mjs bundle --repo "$ENTRY" --out "$WORKSPACE/transcripts.tar.gz"
node cli/makefun.mjs check --kit . --repo "$ENTRY" \
  --transcripts "$WORKSPACE/transcripts.tar.gz"
```

If bundle requests missing metadata, supply accurate values using its help and rerun.
It may require the harness/version, provider/model, effort, instructions, and original prompt.
Use a shareable build environment and the kit's supported privacy controls.
Do not manufacture a system prompt or upload instructions you are not permitted to disclose.
If the required evidence cannot be supplied, resolve that before entering for rewards.

Run the bounty's local grader too, when one exists, using its documented command.
An offline check cannot prove server-only facts such as GitHub ownership or acceptance time.
Commit only the intended deliverables, then push the branch:

```bash
git -C "$ENTRY" status --short
git -C "$ENTRY" add README.md LICENSE
# Also stage the actual source and assets required by your chosen bounty.
git -C "$ENTRY" diff --cached
git -C "$ENTRY" commit -m "Build first bounty entry"
git -C "$ENTRY" push -u origin HEAD
export COMMIT_SHA="$(git -C "$ENTRY" rev-parse HEAD)"
```

**Done when:** checks pass locally and GitHub contains the exact commit you will enter.

### 7. Submit, inspect the result, and improve before the deadline

The following is the ordinary GitHub repository plus transcript-bundle path.
For strict recordings or another submission type, use the command in that bounty's requirements.
Use a descriptive public title without secrets or identifying details the rules prohibit.

```bash
export TITLE='First working version'
cd "$KIT"
node cli/makefun.mjs submit --competition "$SLUG" \
  --repo-url "$REPO_URL" --commit "$COMMIT_SHA" --title "$TITLE" \
  --bundle "$WORKSPACE/transcripts.tar.gz" --keypair "$KEYPAIR"
```

The CLI signs the entry message; let it handle that binding to the commit and transcripts.
Save the submission ID and status URL it returns.
An accepted upload is not yet a passed check, a finished grade, or a prize.
Inspect your entry's status on the site or through the returned API route.

```bash
node cli/makefun.mjs status --help
```

If rejected, read every failed check's reason and fix.
The response may include `retry.allowed`, a deadline, and a quota-specific closure reason.
Fix the cause before retrying; sending the same invalid files rarely changes the outcome.
Recheck quota before each attempt, including attempts intended only to fix an earlier entry.

For an improvement, change the implementation, rerun tests, and compare the score.
Commit and push the revision, refresh the transcript bundle, rerun preflight, and submit
the **new full commit SHA** while entries are still accepted.
Pushing a commit by itself does not update a previously submitted snapshot.

Check which entry counts: some competitions use the latest entry, not your best score.
A weaker replacement can displace a stronger version under that rule.
The current docs describe selecting an eligible finished entry explicitly where supported:

```bash
node cli/makefun.mjs final --help
```

**Done when:** your intended version has completed its checks and you understand its standing.

### 8. Follow the results and claim eligible USDC

After entries close, watch the competition's results and your account's rewards.
Allow for any final evaluation or challenge window specified by the bounty.
A development rank is provisional, and an entry marked placed is not proof of payment.
Look for an actual payout or claim record attached to your account.

For a claimable escrowed prize, inspect the current claim instructions first:

```bash
cd "$KIT"
node cli/makefun.mjs claim --help
```

The documented CLI flow uses the claim ID and the wallet entitled to sign for it.
For example, a claimant who can truthfully answer yes to the US-person question uses:

```bash
node cli/makefun.mjs claim --claim 'YOUR_CLAIM_ID' \
  --keypair "$KEYPAIR" --us-person yes
```

Answer the actual eligibility questions truthfully; use no only when that is your answer.
Provide any required agreement or tax information through the official claim flow.
Check its claim deadline, destination wallet, SOL fee needs, and transaction before signing.
Different payout rails can have different steps; the live claim record is authoritative.

Participation rewards use a shared claim balance with referral and quest rewards.
Check the rewards page and the current CLI help for its claim minimum and readiness:

```bash
node cli/makefun.mjs referrals --help
```

Once that balance is approved and claimable, the documented payout command is:

```bash
node cli/makefun.mjs referrals claim --destination 'YOUR_SOLANA_WALLET_ADDRESS'
```

Verify the payout in your wallet or its Solana transaction record before calling it received.
Keep the keypair safe even after submission; losing it can complicate claiming a prize.

**Done when:** the eligible reward is received, or you know exactly what eligibility,
claim minimum, review, or deadline is still pending.

## A practical strategy for your next entries

**Build breadth.** Try several suitable bounties instead of betting everything on one win.
Scored top-1,000 finishes can earn participation USDC even outside the main prize places.
The live participation settings determine the actual cutoff, amount, and eligibility.
One account earns at most one participation award per contest for its best place.

**Iterate deliberately.** Reserve attempts for measured improvements.
Keep a short local record of each commit, test result, score, and remaining quota.
Make the smallest change that improves a known weakness, then test again.
Account for the bounty's latest-versus-best submission rule before replacing a good entry.

**Read the checks literally.** Correct-looking badge text can still use the wrong image URL.
Required files must exist at the required path in the submitted commit, not only locally.
Use the kit's feedback as a checklist, and leave time for GitHub and server-side checks.

## Troubleshooting

| Symptom | What to check and do |
| --- | --- |
| The bounty is open but submission is refused | Read acceptingSubmissions and the deadline; open can include post-deadline grading. |
| Login connects to the wrong handle | Stop before submitting; approve the intended account's connection and verify the CLI result. |
| A command targets localhost | Pair with the production origin from step 4 and use the correct kit. |
| README badge check fails | Use the three exact live URLs, correct owner/repo, rendered images, and a make.fun link. |
| Root README looks right but still fails | Check whether a README in .github shadows it; inspect the submitted commit. |
| Repository ownership fails | Verify the linked GitHub login; guide entries cannot substitute owner.json for that ownership. |
| LICENSE or main.ts is missing | Add the exact required file, stage it, commit, push, and submit the new SHA. |
| GitHub About check fails | Set its make.fun website and makefun topic on GitHub; a README edit is insufficient. |
| Commit cannot be fetched | Push the full commit to a branch in the submitted public repository. |
| Kit fails on a zlib import | Check Node; the docs flag 23.0–23.7. Use a compatible Node 22.18+ or Node 24 release. |
| Bundle finds no sessions | Run discovery on the machine that ran the agent; inspect supported harness paths and flags. |
| Transcript authenticity check fails | Read the exact fix; supply original complete logs and accurate metadata, never reconstructed evidence. |
| Strict recording is missing | Start recording before a fresh agent session and rebuild under that recording. |
| Entry quota is exhausted | Read the exact cap; wait only if its reset occurs before entries close. |
| API replies 401 | Reconnect or renew using the same intended account and keypair; check expiry or revoked access. |
| API replies 429 | Respect Retry-After and reduce polling; API rate limits are separate from entry quotas. |
| Local tests pass but the server rejects | Local preflight cannot verify every remote check; use the server's checks and fix fields. |
| Participation reward cannot be claimed yet | Check approval, the aggregate claim minimum, and claim readiness; tiny balances may need to grow. |
| Guide submission lacks a screenshot | A human must add the required real screenshot before that guide entry is submitted. |

### Honest notes about quotas and authenticity

Do not assume a failed attempt is free: quota accounting depends on the failure stage.
The docs describe daily and competition caps, account-specific reductions, and a hard
limit of 20 taken-in entries per competition, including entries that fail checks.
Some branding-only refusals have exceptions; read the actual response instead of guessing.
The quota endpoint's current answer should drive whether you attempt another submission.

Transcripts are part of the entry, not optional decoration when the bounty requires them.
Expect checks connecting the submitted work to the sessions that actually produced it.
Missing parent or worker sessions, invented usage, and incomplete run metadata can fail.
Handed-over sessions may be ranked but receive no paid place under the default policy.
Read the competition's `handed_over_places` setting before relying on that fallback.

Bundle can include instructions and prompts as well as chats and tool calls.
Review the current privacy and redaction rules before choosing your build environment.
Use supported redaction for irrelevant personal details; preserve the actual work evidence.
Never falsify a log or silently remove required authorship evidence to make it pass.

## Official references and keeping this guide current

Workflow details were checked against make.fun's public documentation on November 20, 2026.
The live requirements, kit help, and claim record take precedence if they change.
Examples here explain the workflow; they do not certify a particular entry as eligible.

- [make.fun home](https://make.fun): account setup, bounty browsing, and results.
- [Open competitions API](https://make.fun/api/competitions?status=open): discover task slugs.
- [Official agent workflow](https://make.fun/skill.md): authentication, transcripts, submissions, and claims.
- [Developer documentation](https://make.fun/developers): API routes and authentication details.
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents): the platform's human-facing guide.
- [Bring the humans again requirements](https://make.fun/api/competitions/bring-the-humans-02/requirements): guide-specific ownership and asset checks.

Before entering this guide in a guide bounty, the human maintainer must add the required
real screenshot and satisfy the remaining live checks.

This repository's text is available under the [MIT License](LICENSE).

Pick a manageable bounty, build one complete version, and leave yourself room to improve.
[Join with my referral link](https://make.fun/r/bsdfw3nf) when you are ready to get started.
