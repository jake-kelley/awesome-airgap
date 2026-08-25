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
| **[Buttercup](https://github.com/buttercup/buttercup-desktop)** | GPL-3.0 with 4.4k stars, and fully offline in principle, but the desktop repo was archived by its maintainers in March 2025. An archived password manager is not a credible place to keep credentials. |
| **Cuckoo Sandbox** | Original upstream is dormant. Superseded by its actively maintained fork [CAPEv2](https://github.com/kevoreilly/CAPEv2). |


### Source-available only

These are widely deployed Splunk apps with no published source repository. Distribution is Splunkbase-only, so the license cannot be audited and the project cannot be assessed against rule 3. That is a verification failure rather than a judgment on the software.

| Tool | Reason |
|---|---|
| **Splunk Add-on for Microsoft Windows / Unix and Linux / Sysmon** | No source repo under the `splunk` org. Splunkbase-only distribution under Splunk's own license, which is not OSI-approved. |
| **Splunk Common Information Model (CIM) app** | Same: no published source. [CIM Vladiator](https://github.com/hire-vladimir/SA-cim_vladiator) is listed instead as the open tooling around CIM. |
| **Splunk Security Essentials** | No source repo at any Splunk-org path, and no credible maintained third-party fork. |
| **Splunk Machine Learning Toolkit (the app)** | No published source. Its open-source container companion, [DSDL](https://github.com/splunk/splunk-mltk-container-docker), is listed. |
| **Splunk Lookup Editor** | Long-standing and genuinely useful, but no current source repo under the author's account. Splunkbase-only. |

### Too new to judge

| Tool | Reason |
|---|---|
| [rcastley/splunk-custom-visualizations](https://github.com/rcastley/splunk-custom-visualizations) | Apache-2.0, 41 stars, vendored JS with no CDN calls, and genuinely the best community answer to Dashboard Studio custom visualizations. Created 2026-03-17, which fails the "not brand new" half of rule 3. Revisit in a year; it is the strongest candidate on this page. |

### Out of scope

Genuinely open, genuinely air-gap-capable, and genuinely not what this list is for. Recorded so nobody re-proposes them.

| Tool | Reason |
|---|---|
| **GNOME Keyring**, **KWallet** | Both are fully offline encrypted credential stores, packaged in Debian main. They are desktop-environment plumbing that arrives with the OS and that nobody administers as a security control. The list's scope rule excludes general-purpose infrastructure that happens to be useful, and these are the clearest case of it. |
| **Seahorse** | GTK front end for GPG and SSH key management on top of GNOME Keyring. Real security function, but it is a desktop GUI for [GnuPG](https://gnupg.org/), which is listed directly and is what an offline signing workflow actually uses. |
| **[BIRD](https://gitlab.nic.cz/labs/bird)** | GPL-2.0, in Debian main, needs zero internet, and would qualify on every hard rule. It is a BGP and OSPF routing daemon whose security relevance is one optional feature (RPKI route-origin validation) rather than its purpose. Revisit if route-origin validation on isolated backbones becomes a category of its own. |
| **[Tor](https://gitlab.torproject.org/tpo/core/tor)** | BSD-3-Clause and unambiguously open, but it fails the air-gap rule at the concept level. Its entire function is routing through relays operated by strangers on the public internet. There is no upstream feed that substitutes for a relay network. Unlike the live-feed dependencies elsewhere on this list, Tor does not degrade offline, it stops. |

### Canonical home is on GitHub after all

Checked during the non-GitHub sweep and found to develop on GitHub despite a project-branded website. Listed so the question is not reopened: strongSwan, OpenVPN, Snort 3, AIDE, rsyslog, syslog-ng, LibreSSL portable, VeraCrypt, nwipe, and scrub. In each case the project site is a download or commercial-support front end and the live repository is on GitHub.

---

## Qualified, but cut

Every tool here clears all three rules. They were cut only to avoid stacking three or four overlapping entries in one subcategory. One exception is noted inline: Splunk Gantt was cut for age rather than redundancy. If you think one of these earns a slot over something currently listed, open a pull request and make the argument.

| Tool | Overlaps with | Note |
|---|---|---|
| [Psono](https://github.com/psono/psono-server) | Passbolt | Apache-2.0 self-hosted team password manager, genuinely offline-capable. Cut to avoid stacking two AGPL-style team vaults in one subcategory; Passbolt has roughly sixty times the community. |
| [Padloc](https://github.com/padloc/padloc) | Vaultwarden, Bitwarden | AGPL-3.0, 2.9k stars, clean self-hosted design. Last pushed March 2025, so it is drifting rather than dead. Revisit if activity resumes. |
| [LessPass](https://github.com/lesspass/lesspass) | KeePassXC, gopass | GPL-3.0 and the most air-gap-native design here: passwords are derived from a master phrase and the site name, so there is no vault to sync or carry at all. Cut on merit rather than mechanics. Deterministic derivation cannot rotate a single credential without changing the master, cannot store a password someone else assigned you, and turns a master-phrase compromise into the loss of every account at once. Listed here so the tradeoff is on record rather than the tool looking overlooked. |
| [OSV-Scanner](https://github.com/google/osv-scanner) | Trivy, Grype, Dependency-Track | Offline-capable once the DB is exported locally. Strong tool, crowded category. |
| [detect-secrets](https://github.com/Yelp/detect-secrets) | Gitleaks, TruffleHog, Kingfisher | Fully offline, plugin-based, with a useful baseline-diff workflow. |
| [git-secrets](https://github.com/awslabs/git-secrets) | Gitleaks | Fully offline pre-commit hook, but a much narrower feature set. |
| [KICS](https://github.com/Checkmarx/kics) | Checkov | Fully offline IaC scanner with bundled queries. |
| [Terrascan](https://github.com/tenable/terrascan) | Checkov | Same rationale as KICS. |
| [cfssl](https://github.com/cloudflare/cfssl) | step-ca | Fully offline PKI toolkit. step-ca covers CA plus ACME plus an offline CLI mode in one entry. |
| [in-toto](https://github.com/in-toto/in-toto) | Cosign | Offline-capable supply-chain attestation, largely covered by Sigstore attestations here. |
| [Splunk Gantt](https://github.com/alexiri/splunk-gantt) | (no listed alternative) | GPL-3.0, 11 stars, d3 vendored rather than CDN-loaded, so it clears every hard rule. Cut on judgment: last pushed 2017, and Splunk's custom-visualization API has moved since. Fork-and-patch, not drop-in. |
| [Knot Resolver](https://gitlab.nic.cz/knot/knot-resolver) | BIND 9 | GPL-3.0, in Debian main, DNSSEC-validating recursive resolver from the CZ.NIC team. Cut as redundant: BIND 9's resolver mode covers the same ground and needs the same mirrored root zone. |
| [rkhunter](https://sourceforge.net/projects/rkhunter/) | Samhain, chkrootkit | Last release 1.4.6, dated 2018-02-24. Eight years stale with no maintained successor off GitHub. Failed rule 3 rather than being cut for redundancy, but recorded here since it is otherwise a reasonable ask. |
| [ntopng](https://github.com/ntop/ntopng) | Zeek, Suricata | GPL-3.0, fully self-hosted NDR. Cut for category budget only. |
| [Dionaea](https://github.com/DinoTools/dionaea) | Cowrie, OpenCanary | Self-hosted malware-capture honeypot. |
| [GRR Rapid Response](https://github.com/google/grr) | Velociraptor | Similar live remote forensics ground, less recent activity. |
| [Yeti](https://github.com/yeti-platform/yeti) | MISP, OpenCTI | Same ground, smaller community. |
| [IntelMQ](https://github.com/certtools/intelmq) | MISP | AGPL-3.0 self-hosted feed-automation pipeline. |
| [RustScan](https://github.com/bee-san/RustScan) | Nmap, Masscan | Fast port scanner, redundant with two already listed. |
| [DevSec Hardening Framework](https://github.com/dev-sec) | OpenSCAP, ComplianceAsCode, Lynis | Apache-2.0 and air-gap friendly, but it is Ansible content rather than a standalone tool. |
| [Semgrep Community Edition](https://github.com/semgrep/semgrep) | Opengrep | Still usable and largely offline-capable, but cross-file taint analysis moved behind the paid tier in 2024–25. Opengrep restores it under a fully open, multi-vendor-governed license. |
