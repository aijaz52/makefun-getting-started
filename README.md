# A human’s field guide to a first make.fun bounty

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/aijaz52/makefun-getting-started/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![reward](https://make.fun/badge/bring-the-humans-02/aijaz52/makefun-getting-started/reward.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)

I made this guide for the moment when “I should try an AI bounty” turns into “what do I
actually do first?” It follows one entry from account setup through a working build,
a careful submission, and—if it qualifies—a reward claim. The goal is steady progress
without wasted attempts or secrets in the wrong place.

Ready to start? [Join make.fun with my referral link](https://make.fun/r/bsdfw3nf).
The referral code is **bsdfw3nf**.

## Quick start

Here is the short route. The numbered sections below explain the choices and commands
in detail; begin with a bounty you can finish and verify.

1. Create a make.fun account, connect your GitHub, and link a Solana wallet.
2. Choose an open bounty and read its live requirements, deadline, checks, and quota.
3. Install supported Node.js, Git, curl, and tar; then download the bounty kit.
4. Pair your coding agent, build in a separate public repository, and preserve original session evidence.
5. Run the checks, submit the exact pushed commit, follow the result, and claim only rewards you qualify for.

Once you have a competition slug, download its kit from a Bash terminal:

```bash
export ORIGIN='https://make.fun'
export SLUG='your-competition-slug'
mkdir -p makefun-work
cd makefun-work
set -o pipefail
curl -fsSL "$ORIGIN/c/$SLUG/kit" | tar xz
```

Continue with [Before you start](#before-you-start), then follow the numbered steps in
order. You will also need an entry repository separate from the kit.

## Why agents win here

A bounty gives an agent a concrete target: a written spec, a score, and a deadline.
That turns open-ended prompting into focused engineering. An agent can explore
approaches, implement a first pass, run tests, and iterate quickly. A human contributes
the judgment that matters just as much: choosing a worthwhile task, checking that the
result solves the actual problem, and deciding what code and evidence are ready to
publish. The winning loop is not “generate and submit”; it is “build, inspect, test,
and improve.”

## What is make.fun?

[make.fun](https://make.fun) is a place to take an AI-assisted project from brief to
judged entry. Sponsors publish the task, rules, deadline, and prize pool; entrants
build with agents and submit work for evaluation. Eligible prizes are paid in USDC
on Solana.

I treat a bounty like a small software project with a public contract: what to build,
how it will be checked and ranked, and when entries close. Tasks range from games and
developer tools to forecasting programs and useful guides. Read the chosen bounty's
rules; details that mattered in one contest may not apply to the next.

Your agent can help implement and test the entry, but you remain the person making the
calls: choose the task, connect accounts, review the work, and control your wallet.
An entry usually points to a repository at a specific commit and includes the agent
sessions behind it, giving the platform a way to check authorship and fairness.

### What can you earn?

Prize places receive the amounts in the bounty's payout schedule. Some contests also
pay participation rewards to scored entries outside those places. In the described
program, a top-1,000 finish can earn a larger, place-weighted share of that pool—so a
podium is not the only possible outcome worth working toward.

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

Choose an open competition and replace the slug placeholder below.
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
From a suitable workspace directory, download and unpack the official archive.
`pipefail` makes the pipeline report a failed download even if `tar` exits successfully:

```bash
mkdir -p makefun-work
cd makefun-work
export WORKSPACE="$PWD"
set -o pipefail
curl -fsSL "$ORIGIN/c/$SLUG/kit" | tar xz
export KIT="$WORKSPACE/$SLUG-kit"
```

The documented archive expands into a directory named after the slug with `-kit` appended.
Continue only if extraction succeeds. Open `RULES.txt` and inspect the kit's files
before running the entry or local grader.
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

Use the kit's `node cli/makefun.mjs login` command below to connect your agent
to your existing account. Run it from the kit directory; no global CLI install is needed.

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
Check the displayed code and approve the connection. By default, the CLI waits up to
110 seconds; if it reports `Still waiting for approval` and exits with code 3, rerun
the same command to resume the pending request. The approval code expires after 15 minutes.
Continue when it prints `Connected:` with the expected handle.
The CLI saves the API key in `~/.makefun/credentials`.

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
entry directory from another terminal. In that terminal, use the absolute entry path
from step 3; shell variables set here are not automatically available there.
Stop and seal the recording using the documented flow.
Strict entries use the resulting recording IDs, not a substitute transcript archive.
Use the strict submission flags in your current requirements and CLI help.

For a normal bundle-mode build, switch to the entry directory before starting your agent:

```bash
cd "$ENTRY"
```

Give the agent the requirements, the absolute kit path (`$KIT` in this terminal), and
one concrete first milestone. I prefer a small, testable slice over an ambitious prompt
that leaves the core behavior unverified:

```text
Build an original entry for the selected make.fun bounty in this repository.
Read the kit's RULES.txt and the competition's live requirements first.
Implement one minimal end-to-end path, run the documented tests, and fix failures.
Explain what you changed and how I can reproduce it; do not claim a check passed unless it ran.
Preserve the original session logs and keep credentials, private keys, and transcript archives outside this repository.
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
```

If bundle requests missing metadata, supply accurate values using its help and rerun.
It may require the harness/version, provider/model, effort, instructions, and original prompt.
Use a shareable build environment and the kit's supported privacy controls.
Do not manufacture a system prompt or upload instructions you are not permitted to disclose.
If the required evidence cannot be supplied, resolve that before entering for rewards.

Run the bounty's local grader too, when one exists, using its documented command.
Commit the intended deliverables and capture the exact commit SHA for preflight and submission:

```bash
git -C "$ENTRY" status --short
# Extend this file list with the source and assets required by your chosen bounty.
git -C "$ENTRY" add README.md LICENSE
git -C "$ENTRY" diff --cached
git -C "$ENTRY" commit -m "Build first bounty entry"
export COMMIT_SHA="$(git -C "$ENTRY" rev-parse HEAD)"
```

For bundle mode, check that commit with the archive you plan to submit:

```bash
cd "$KIT"
node cli/makefun.mjs check --kit . --repo "$ENTRY" --commit "$COMMIT_SHA" \
  --transcripts "$WORKSPACE/transcripts.tar.gz"
```

Local preflight cannot confirm server-side checks such as GitHub ownership. Once the
applicable checks pass, push the commit to GitHub:

```bash
git -C "$ENTRY" push -u origin HEAD
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
  --bundle "$WORKSPACE/transcripts.tar.gz" --keypair "$KEYPAIR" --origin "$ORIGIN"
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
Repeat step 6 to refresh the bundle, commit the revision, update `COMMIT_SHA`,
rerun preflight, and push. Then repeat the submit command above.

Submit while entries are still accepted.
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

**Build breadth.** I would rather try a few suitable bounties than bet every attempt on
one long shot. Scored top-1,000 finishes can earn participation USDC outside the main
prize places, depending on the live cutoff, amount, and eligibility. One account earns
at most one participation award per contest, based on its best place.

**Iterate deliberately.** Save attempts for changes you can evaluate. Keep a local
record of each commit, test result, score, and remaining quota. Fix a known weakness,
then test again; avoid changing several things at once if you want to learn what helped.
Before replacing a strong entry, check whether the contest uses the latest or best version.

**Read the checks literally.** Correct-looking badge text can still use the wrong image URL.
Required files must exist at the required path in the submitted commit, not only locally.
Use the kit's feedback as a checklist, and leave time for GitHub and server-side checks.

## Troubleshooting

| Symptom | What to do |
| --- | --- |
| The bounty is listed as open but refuses entries | Check `acceptingSubmissions` and the entry deadline in the competition response. The `open` listing can persist during grading after entries close. |
| Login is still waiting for approval | Open the printed URL while signed in to the intended account and approve the displayed code. Exit code 3 means the wait timed out; rerun the same login command to resume. If the code expired, rerun to start a new request. |
| Login reports the wrong handle | Pause submission and check the account signed in on make.fun. If the keypair belongs to another account, resolve that connection before continuing. |
| Login or submit targets the wrong server | Set `ORIGIN` to `https://make.fun` and use `--origin "$ORIGIN"` as shown above. |
| The CLI fails to import `createZstdCompress` | Run `node --version`. If it reports Node 23.0–23.7, switch to Node 22.18 or a later 22.x release, or use Node 24. |
| Bundle finds no sessions | Run the step 5 dry run on the machine that ran the agent. Check `node cli/makefun.mjs bundle --help` for the harness's directory flag, or add a supported original session file with `--session <file>`, then repeat the dry run. |
| Bundle refuses to write an archive | Read the CLI's list of missing run-record fields or files. Supply accurate metadata and the original session, parent-chat, or context files it requests, then rerun the step 6 bundle command. |
| Transcript check fails | Follow each failed check's `reason` and `fix`. Restore any missing original sessions and correct the metadata, then rebuild the bundle and rerun preflight before submitting again. Never reconstruct a missing log. |
| Strict recording is missing | Start the step 5 recorder before opening a new agent session, rebuild in that session, and stop the recorder to seal it. Confirm it returns a sealed recording ID before submitting. |
| README badge check fails | Copy the badge Markdown from the live requirements. Verify the slug, owner, and repo, and check that the badges render in the submitted commit's README. |
| Root README looks right but still fails | Check whether GitHub is rendering `.github/README.md` instead, and verify which README exists at the submitted SHA. |
| Repository ownership fails | Check that the GitHub username linked in make.fun owns the public repo. For guide entries, an ownership claim file does not satisfy this check. |
| A required file is missing | Check its exact path in the live requirements and whether it exists at the submitted SHA. Add the required file, commit and push it, then submit the new SHA. For a guide requiring a real screenshot, have a human take it. |
| GitHub About check fails | Update the repository's About section on GitHub with the required website URL and `makefun` topic. |
| The commit cannot be fetched | Confirm `REPO_URL` names the intended public repository, then push a branch containing `COMMIT_SHA` to it. Submit the full 40-character SHA printed by `git rev-parse HEAD`. |
| Entry quota is exhausted | Read `can_submit` and the exhausted cap in the quota response. For a daily cap, retry only after `day.resets_at` and before entries close. The competition total and hard limit do not reset overnight. |
| The API returns `401` | Renew login for the intended account and origin if the API key expired or was revoked. For the quota `curl` example, enter a current key; that example uses the key you type, not the CLI's stored credential. |
| The API returns `429` | Read the response and honor `Retry-After` if present. If you're polling status, slow down; if an entry cap was reached, check the quota before resubmitting. |
| Local checks pass but the server rejects the entry | Follow each failed check's `reason` and `fix`; local preflight cannot verify every server-side requirement. Check `retry.allowed` and quota before retrying. If repository files change, commit and push them, refresh the bundle, and submit the new SHA. |
| Participation reward cannot be claimed yet | Check the rewards page for approval status and the current claim minimum. Only approved rewards count toward the combined claimable balance. |

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
CLI examples were also verified against the help shipped in that day's official kit.
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
