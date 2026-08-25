# Not included

Tools that were evaluated and left out, and why. Read this before opening an issue asking why something is missing.

Two kinds of exclusion below. The first list failed a hard rule and will not be added. The second list passed every rule but was cut to keep categories from filling with near-duplicates, and those are fair game for a pull request if you can argue the case.

---

## Failed a hard rule

### Not open source

| Tool | Reason |
|---|---|
| **HashiCorp Vault** | Relicensed from MPL-2.0 to the Business Source License in August 2023. Not OSI open source. Use [OpenBao](https://github.com/openbao/openbao) instead, which is the Linux Foundation-governed fork of the last MPL release. |
| **Graylog** | SSPL-1.0. The Server Side Public License was explicitly rejected by the OSI, so it fails the open-source rule despite being free to self-host. |
| **Chef InSpec** | Relicensed from Apache-2.0 to the Business Source License in 2023. |
| **Elastic detection-rules** | Elastic License 2.0 — source-available, not OSI open source. [Sigma](https://github.com/SigmaHQ/sigma) covers detection-as-code instead. |
| **PingCastle** | The free "Open Source Edition" uses the Non-Profit OSL 3.0, which forbids for-profit use of the software without a separate paid license. That rules out using it on a paid engagement. |
| **SysReptor** | Custom community license forbidding redistribution and derivative works. Free to self-host, but not open source. |

### Crippled free tier

| Tool | Reason |
|---|---|
| **Nessus** | Free "Essentials" is capped at 16 hosts and phones home to Tenable for activation and plugin updates. Not usable at real scale. |
| **Metasploit Pro** | Closed-source and license-gated. The [Framework](https://github.com/rapid7/metasploit-framework) is included; Pro is not. |
| **Cobalt Strike** | Fully commercial, license-key gated. No free tier exists. |
| **Burp Suite** | Community Edition has no automated scanner at all; Pro is commercial and performs periodic online license checks. [OWASP ZAP](https://github.com/zaproxy/zaproxy) covers this ground. |
| **CIS-CAT** | The free "Lite" edition runs only a small subset of benchmarks and is not open source. The full tool needs a paid CIS SecureSuite membership. |
| **Snyk / GitGuardian** | Primarily SaaS. The free CLI components are rate-limited gateways into a paid cloud backend rather than full-capacity offline tools. |
| **TheHive** | The open-source 3.x/4.x line was archived in December 2025. TheHive 5 is commercial-only. [DFIR-IRIS](https://github.com/dfir-iris/iris-web) fills the gap. |

### Cannot run air-gapped

| Tool | Reason |
|---|---|
| **T-Pot** | Its own documentation requires a working outbound connection for installation and operation, and it pulls container images on every start. No supported air-gap path, only unsupported community workarounds. |
| **Maltego (Community)** | Requires online account activation and most transforms call live web APIs. Fails the air-gap rule twice over. |
| **theHarvester / Recon-ng** | Genuinely open source, but both are built entirely around live queries to external search engines and APIs. They do not meaningfully function against local data, which is the only kind of OSINT this list can cover. |
| **kube-hunter** | Offline-capable in internal mode, but the headline remote mode is built to probe from outside the cluster and some flows report to a vendor-hosted dashboard by default. Falco, Kyverno and kube-bench cover this ground without the ambiguity. |

### Unmaintained or superseded

| Tool | Reason |
|---|---|
| **AlienVault OSSIM** | Effectively unmaintained, folded into a commercial product. Not a credible current option. |
| **Cuckoo Sandbox** | Original upstream is dormant. Superseded by its actively maintained fork [CAPEv2](https://github.com/kevoreilly/CAPEv2). |

---

## Qualified, but cut

Every tool here clears all three rules. They were cut only to avoid stacking three or four overlapping entries in one subcategory. If you think one of these earns a slot over something currently listed, open a pull request and make the argument.

| Tool | Overlaps with | Note |
|---|---|---|
| [OSV-Scanner](https://github.com/google/osv-scanner) | Trivy, Grype, Dependency-Track | Offline-capable once the DB is exported locally. Strong tool, crowded category. |
| [detect-secrets](https://github.com/Yelp/detect-secrets) | Gitleaks, TruffleHog, Kingfisher | Fully offline, plugin-based, with a useful baseline-diff workflow. |
| [git-secrets](https://github.com/awslabs/git-secrets) | Gitleaks | Fully offline pre-commit hook, but a much narrower feature set. |
| [KICS](https://github.com/Checkmarx/kics) | Checkov | Fully offline IaC scanner with bundled queries. |
| [Terrascan](https://github.com/tenable/terrascan) | Checkov | Same rationale as KICS. |
| [cfssl](https://github.com/cloudflare/cfssl) | step-ca | Fully offline PKI toolkit. step-ca covers CA plus ACME plus an offline CLI mode in one entry. |
| [in-toto](https://github.com/in-toto/in-toto) | Cosign | Offline-capable supply-chain attestation, largely covered by Sigstore attestations here. |
| [ntopng](https://github.com/ntop/ntopng) | Zeek, Suricata | GPL-3.0, fully self-hosted NDR. Cut for category budget only. |
| [Dionaea](https://github.com/DinoTools/dionaea) | Cowrie, OpenCanary | Self-hosted malware-capture honeypot. |
| [GRR Rapid Response](https://github.com/google/grr) | Velociraptor | Similar live remote forensics ground, less recent activity. |
| [Yeti](https://github.com/yeti-platform/yeti) | MISP, OpenCTI | Same ground, smaller community. |
| [IntelMQ](https://github.com/certtools/intelmq) | MISP | AGPL-3.0 self-hosted feed-automation pipeline. |
| [RustScan](https://github.com/bee-san/RustScan) | Nmap, Masscan | Fast port scanner, redundant with two already listed. |
| [DevSec Hardening Framework](https://github.com/dev-sec) | OpenSCAP, ComplianceAsCode, Lynis | Apache-2.0 and air-gap friendly, but it is Ansible content rather than a standalone tool. |
| [Semgrep Community Edition](https://github.com/semgrep/semgrep) | Opengrep | Still usable and largely offline-capable, but cross-file taint analysis moved behind the paid tier in 2024–25. Opengrep restores it under a fully open, multi-vendor-governed license. |
