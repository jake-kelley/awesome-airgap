# Contributing

Pull requests welcome. The bar is deliberately high, because a list where half the entries quietly need internet is worse than no list at all.

## Before you open a PR

Check [NOT-INCLUDED.md](NOT-INCLUDED.md) first. The tool may already have been evaluated and cut, and the reason is recorded there.

## The three rules

A tool must clear all three.

**1. Open source and free in major or full capacity.**
Open-core is acceptable only where the free tier is genuinely useful at real scale. If something is paywalled, say so in the entry. A free tier that exists to sell you the paid one does not qualify. Source-available licenses that are not OSI-approved (BUSL, SSPL, Elastic License) do not qualify.

**2. Deployable air-gapped.**
The tool gets zero internet access and must still work in major or full capacity. An upstream mirror may carry updates inward: signature packs, vulnerability databases, rule sets, container images. Disqualifying: a cloud control plane, a mandatory license check, a SaaS backend, online-only activation.

**3. A real project, if GitHub-hosted.**
Not brand new. More than 10 stars. Preferably more than two contributors, though that one is a preference rather than a rule.

## Entry format

Match the existing entries exactly:

```markdown
**[Tool Name](https://github.com/org/repo)** — LICENSE · Form · N★ · since YEAR
One or two sentences on what it does, and what makes it worth a slot over its alternatives.
> `FULL` — What is and is not needed offline.
```

- **License** — SPDX identifier where one applies. If the license is unusual, say what it actually permits.
- **Form** — how it is delivered. `Go single binary`, `Docker Compose`, `Python CLI`, `static HTML`, `Splunk app`.
- **Stars** — rounded, as verified on the day you submit.
- **Rating** — `FULL`, `MAJOR`, or `CONDITIONAL`. See the table in the README.

## The air-gap verdict is the whole point

This is the part reviewers will push back on. "Works offline" is not a verdict. Name the specific thing that breaks and the specific thing the mirror has to carry.

Good:

> `MAJOR` — The scan engine works fully offline. Signatures refresh via `freshclam`, which needs a mirrored feed or the database simply goes stale.

Not good:

> `MAJOR` — Mostly works offline with some limitations.

If a tool has an officially documented air-gap installation path, say so and link it. That is a meaningful quality signal and there are not many of them.

## Verify, don't assume

Star counts, licenses, and air-gap behavior all need to be checked against the source, not recalled. Vendor marketing pages routinely describe an offline mode that turns out to mean "syncs every 24 hours." If you cannot confirm something, say so in the PR rather than guessing.

## Scope

In scope: tools a security team runs. Detection and response, offense and assessment, application and supply-chain security, cloud security tooling that does not depend on a vendor SaaS, and portable offline utilities.

Out of scope: general-purpose infrastructure that happens to be useful, hosted services of any kind, and anything whose primary interface is a web dashboard someone else operates.
