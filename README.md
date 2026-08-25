# awesome-airgap

> Security tooling that survives the sneakernet.

A curated list of **75 open-source security tools that work with zero internet access.**

Every other security tool list assumes you can reach the internet. Plenty of teams can't. Disconnected labs, isolated OT and ICS networks, forensic workstations, regulated enclaves, offline build farms, incident response on a network you've just pulled the plug on — in all of them the constraint is the same, and most tooling quietly assumes it away.

So the question this list answers for every entry is not "is this a good tool?" It is **"what exactly breaks when you unplug it, and what does the upstream mirror have to carry so it doesn't?"**

Nothing here is a demo of a paid product. Nothing here phones home to a vendor to function.

---

## Inclusion criteria

A tool earns a slot only if it clears all of these:

1. **Open source and free in major or full capacity.** Open-core is allowed only where the free tier is genuinely useful at real scale. Where something is paywalled, the entry says so. Crippled free tiers are rejected and listed in [NOT-INCLUDED.md](NOT-INCLUDED.md) with the reason.
2. **Deployable air-gapped.** The tool itself gets zero internet access and must still work in major or full capacity. An upstream mirror may carry updates inward — signature packs, vulnerability databases, rule sets, container images. A cloud control plane, a mandatory license check, a SaaS backend, or online-only activation is disqualifying.
3. **Real project, if GitHub-hosted.** Not brand new, more than 10 stars, and preferably more than two contributors.

Portable single binaries (Go, Rust, C) and static offline web apps are actively preferred. Splunk apps and add-ons are welcome where fully free.

## Air-gap ratings

| Rating | Meaning |
|---|---|
| `FULL` | Works completely offline. Nothing to mirror beyond the software itself. |
| `MAJOR` | Core function works offline. One or more secondary features need a mirrored feed or must be disabled. The entry names which. |
| `CONDITIONAL` | Works offline only after a specific setup step — staging a database, pre-bundling dependencies, or standing up a private backend. The entry names the step. |

Star counts and licenses were verified against the GitHub API on **2026-08-25**.

---

## Contents

- [Detection & Response](#detection--response)
  - [SIEM & log pipelines](#siem--log-pipelines) · [SOC & NOC monitoring](#soc--noc-monitoring) · [EDR & host agents](#edr--host-agents) · [Incident response & case management](#incident-response--case-management) · [Digital forensics](#digital-forensics) · [Malware analysis & reverse engineering](#malware-analysis--reverse-engineering) · [Threat intelligence](#threat-intelligence) · [Network security monitoring](#network-security-monitoring) · [Detection engineering](#detection-engineering) · [Honeypots & deception](#honeypots--deception)
- [Offense & Assessment](#offense--assessment)
  - [Vulnerability scanning & management](#vulnerability-scanning--management) · [Hardening & compliance](#hardening--compliance) · [Exploitation frameworks](#exploitation-frameworks) · [Network discovery](#network-discovery) · [Credential auditing](#credential-auditing) · [Wireless assessment](#wireless-assessment) · [Physical & hardware](#physical--hardware) · [Adversary emulation](#adversary-emulation) · [Active Directory](#active-directory) · [Offline OSINT](#offline-osint) · [Assessment reporting](#assessment-reporting)
- [AppSec, Supply Chain & Cloud](#appsec-supply-chain--cloud)
  - [CI/CD policy gating](#cicd-policy-gating) · [SAST & DAST](#sast--dast) · [Software composition analysis](#software-composition-analysis) · [Secret scanning](#secret-scanning) · [Secrets management](#secrets-management) · [SBOM & supply chain](#sbom--supply-chain) · [Containers & Kubernetes](#containers--kubernetes) · [Infrastructure as code](#infrastructure-as-code) · [AWS security & automation](#aws-security--automation) · [PKI & certificates](#pki--certificates)
- [Portable Offline Utilities](#portable-offline-utilities)

---

# Detection & Response

## SIEM & log pipelines

**[OpenSearch](https://github.com/opensearch-project/OpenSearch)** — Apache-2.0 · Java, Docker/deb/rpm · 13.6k★ · since 2021
Distributed search and analytics engine derived from Elasticsearch, used as the indexing and search tier for log pipelines and SIEM stacks, including Wazuh's indexer. The only fully open-source drop-in now that Elasticsearch itself is source-available.
> `FULL` — Runs entirely self-hosted with no external calls. The mirror carries version and plugin packages only.

**[Vector](https://github.com/vectordotdev/vector)** — MPL-2.0 · Rust single binary · 22.5k★ · since 2018
High-performance observability pipeline that collects, transforms, and routes logs, metrics, and traces between sources (syslog, files, Kafka) and sinks (OpenSearch, Loki, S3). Replaces Logstash or Fluentd at a fraction of the resource cost.
> `FULL` — Agent and aggregator modes are purely local. No phone-home behavior of any kind.

## SOC & NOC monitoring

**[Grafana](https://github.com/grafana/grafana)** — AGPL-3.0 · Go + web UI · 76.4k★ · since 2013
The de facto visualization and alerting layer, pulling from Prometheus, Loki, OpenSearch and dozens of other sources into one pane of glass.
> `MAJOR` — Dashboards, alerting, and data-source queries work offline. The plugin catalog and update checker call grafana.com and must be disabled, with plugins side-loaded from a mirror.

**[Zabbix](https://github.com/zabbix/zabbix)** — AGPL-3.0 · C server/agent + PHP frontend · 6.3k★ · since 2001
Infrastructure and network monitoring with discovery, alerting, and root-cause analysis across servers, network gear, and services. The classic NOC tool.
> `FULL` — Entirely self-hosted server, agent, and database. Two decades of running in disconnected telecom and industrial networks.

## EDR & host agents

**[Wazuh](https://github.com/wazuh/wazuh)** — GPL-2.0 · C/Python manager + agents · 16.7k★ · since 2015
Combined EDR/XDR and SIEM: endpoint agents for file integrity monitoring, log collection, rootkit detection, and active response, correlated centrally with rule-based alerting. The most complete free self-hosted EDR stack available.
> `MAJOR` — Manager, indexer, and agents run fully offline. The CVE detection feed and any cloud lookups (VirusTotal integration) need a mirrored feed or must be disabled.

**[osquery](https://github.com/osquery/osquery)** — Apache-2.0 OR GPL-2.0 · C++ single binary · 23.5k★ · since 2014
Exposes OS state — processes, network connections, installed packages, file hashes — as SQL-queryable tables. The instrumentation layer most detection pipelines build on.
> `FULL` — The agent exposes a local scheduled SQL interface only. Fleet managers are separate optional components and nothing phones home by default.

**[Velociraptor](https://github.com/Velocidex/velociraptor)** — AGPL-3.0 · Go single binary · 4.2k★ · since 2018
Endpoint visibility and DFIR at fleet scale, using its own query language (VQL) to run forensic artifact collection and live hunting across thousands of hosts from one console.
> `FULL` — Server and agents talk only to each other over a self-hosted channel. VQL artifact packs are plain files you mirror in.

## Incident response & case management

**[DFIR-IRIS](https://github.com/dfir-iris/iris-web)** — LGPL-3.0 · Python/Flask + PostgreSQL · 1.5k★ · since 2021
Collaborative IR case management for tracking IOCs, tasks, timelines, and evidence across a team during an investigation. Fills the gap left by TheHive's move to commercial-only.
> `FULL` — Self-hosted web app. The public demo instance is unrelated to running your own.

**[FIR](https://github.com/certsocietegenerale/FIR)** — GPL-3.0 · Python/Django · 2.0k★ · since 2015
Lightweight incident tracking and ticketing from CERT Société Générale, for logging, categorizing, and reporting incidents with dashboards and statistics.
> `FULL` — Self-hosted Django app with no external service calls in core functionality.

## Digital forensics

**[Autopsy / The Sleuth Kit](https://github.com/sleuthkit/autopsy)** — Apache-2.0 · Java desktop + C/C++ CLI · 3.3k★ · since 2001
The most widely used free disk-forensics platform, for examining disk images, recovering deleted files, and building investigative timelines. Twenty-plus years in labs that are routinely disconnected.
> `MAJOR` — Disk and file analysis is entirely local. Ingest modules that check hashes against NSRL or VirusTotal need offline hash sets loaded, or must be disabled.

**[Volatility 3](https://github.com/volatilityfoundation/volatility3)** — VSL-1.0 (source-available, free to use, share, modify) · Python · 4.4k★ · since 2007
Memory forensics framework that extracts processes, network connections, and injected code from RAM captures. The standard free tool for memory triage.
> `FULL` — Pure local analysis. OS symbol packs (ISF files) just need staging ahead of time.

**[Plaso](https://github.com/log2timeline/plaso)** — Apache-2.0 · Python CLI/library · 2.1k★ · since 2012
The "super timeline" engine that extracts and normalizes timestamped events from disk images and artifact sets. The parser behind most free DFIR timeline workflows.
> `FULL` — Parses local artifacts (event logs, browser history, filesystem metadata) with no external calls.

## Malware analysis & reverse engineering

**[YARA](https://github.com/VirusTotal/yara)** — BSD-3-Clause · C library + CLI · 9.8k★ · since 2013
The pattern-matching engine for identifying and classifying malware by string, byte, and boolean signatures. Embedded inside ClamAV, CAPEv2, Velociraptor, and most other tools on this list.
> `FULL` — Local matching engine. Rule packs are text files, mirrorable over any transport.

**[ClamAV](https://github.com/Cisco-Talos/clamav)** — GPL-2.0 · C engine, deb/rpm/Docker · 7.2k★ · since 2002
The only fully free engine-and-signatures antivirus that can be scripted into a pipeline — attachment scanning, upload gateways, file triage — without per-endpoint licensing.
> `MAJOR` — The scan engine works fully offline. Signatures refresh via `freshclam`, which needs a mirrored feed or the database simply goes stale.

**[CAPEv2](https://github.com/kevoreilly/CAPEv2)** — GPL-3.0 · Python + KVM/VirtualBox VMs · 3.4k★ · since 2019
Automated malware sandbox that detonates samples in instrumented VMs, unpacks payloads, extracts C2 configs, and classifies behavior via YARA. The actively maintained successor to Cuckoo.
> `MAJOR` — Analysis VMs deliberately run with network cut off for containment, which is the desired posture anyway. YARA and config-extraction packs need mirroring; community feed integrations stay disabled.

**[Ghidra](https://github.com/NationalSecurityAgency/ghidra)** — Apache-2.0 · Java desktop app · 72.8k★ · since 2019
Software reverse-engineering suite with a disassembler, decompiler, and scripting environment for malware binaries and firmware. The dominant free RE platform.
> `FULL` — Built to run standalone on isolated analysis workstations. Only optional BSim similarity databases and the update check need mirroring or disabling.

## Threat intelligence

**[MISP](https://github.com/MISP/MISP)** — AGPL-3.0 · PHP/Symfony + MySQL · 6.5k★ · since 2012
The standard open-source CTI exchange platform, for storing, correlating, and distributing IOCs and structured threat data with ATT&CK mapping. Widely run in isolated and industrial networks precisely because it doesn't need a live connection.
> `MAJOR` — Event storage, correlation, and the API run fully offline. Feed and warninglist auto-update jobs and inter-instance sync need a mirrored bundle carried in.

**[OpenCTI](https://github.com/OpenCTI-Platform/opencti)** — Apache-2.0 (Community Edition) · Node.js/GraphQL · 9.8k★ · since 2018
Graph-based CTI platform for structuring and visualizing intelligence in STIX2, with integrations to MISP and case management. The Community Edition is genuinely full-featured, not a demo. *(Enterprise Edition paywalls SSO/RBAC extensions and some connectors.)*
> `MAJOR` — The knowledge graph and manual STIX2 import/export work offline. Built-in connectors that pull external feeds need mirrored data drops or must be disabled.

## Network security monitoring

**[Zeek](https://github.com/zeek/zeek)** — BSD-3-Clause · C++ + script engine · 7.9k★ · since 1994
Deep protocol-analysis network monitor that produces structured logs via a scriptable policy engine rather than acting as a signature IDS. The foundation most free NDR stacks are built around.
> `FULL` — Local traffic analysis with no external calls. GeoIP and intel-framework feeds are just files.

**[Suricata](https://github.com/OISF/suricata)** — GPL-2.0 · C engine · 6.6k★ · since 2010
The most widely deployed free IDS/IPS engine, multi-threaded for high-throughput sensors, inspecting traffic against Suricata and Snort format rules.
> `MAJOR` — The engine runs fully offline. Rule updates come via `suricata-update`, which needs a mirrored rule feed — exactly what an upstream mirror is built to carry.

**[Security Onion](https://github.com/Security-Onion-Solutions/securityonion)** — GPL-3.0 · Linux distribution / ISO · 4.8k★ · since 2008
All-in-one NSM and SIEM distribution that deploys and wires together Zeek, Suricata, an analyst dashboard, and log storage.
> `FULL` — Ships an **officially documented and supported air-gap setup mode.** The ISO bundles every package and container image needed, with an official offline-repo workflow for later rule and GeoIP updates. One of very few tools here with a first-class air-gap path rather than a workaround.

## Detection engineering

**[Sigma](https://github.com/SigmaHQ/sigma)** — DRL 1.1 (free to use, share, modify; spec is public domain) · YAML + Python tooling · 10.9k★ · since 2016
The vendor-agnostic format for log-based detections, with tooling to convert rules into Splunk, Elastic, KQL and other query languages. Write once, deploy to whichever SIEM is behind the wire.
> `FULL` — Format and conversion tooling are entirely local. Rule updates are a mirrored git pull or archive.

**[Chainsaw](https://github.com/WithSecureLabs/chainsaw)** — GPL-3.0 · Rust single binary · 3.6k★ · since 2021
Fast hunting through Windows event logs using Sigma rules and custom mappings, built for triage without a SIEM. Brings detection-as-code to offline forensic triage of a single disk image.
> `FULL` — Standalone binary against local EVTX files. Rules are files you mirror in.

## Honeypots & deception

**[Cowrie](https://github.com/cowrie/cowrie)** — BSD-3-Clause · Python/Twisted · 6.5k★ · since 2015
Medium-to-high interaction SSH and Telnet honeypot logging brute-force attempts and full shell interaction, including SFTP and SCP file capture.
> `FULL` — Self-contained with no external dependency by default. The optional LLM response mode is off by default and would need a local model to stay offline.

**[OpenCanary](https://github.com/thinkst/opencanary)** — BSD-3-Clause · Python daemon · 3.0k★ · since 2015
Near-zero-footprint tripwire that emulates common services (SSH, RDP, SMB, MySQL, FTP, HTTP) and alerts the moment anyone touches them. Trivial to scatter across an internal segment.
> `FULL` — Alerts via local syslog or log file by default. The commercial Thinkst Canary hardware is a separate product.

---

# Offense & Assessment

## Vulnerability scanning & management

**[OpenVAS / Greenbone Community Edition](https://github.com/greenbone/gvmd)** — AGPL-3.0 · C, Docker Compose or deb · 373★ (gvmd core; org spans several repos) · since 2005
The most complete open-source network vulnerability scanner: authenticated and unauthenticated scanning, a management daemon, and a web UI backed by a continuously updated feed of vulnerability tests. Originally forked from Nessus when Nessus went proprietary.
> `MAJOR` — Scanner and manager need zero runtime internet once deployed. `greenbone-feed-sync` accepts a custom feed URL over rsync or http, so an internal mirror can carry the NVT, SCAP, CERT, and Notus feeds inward on a schedule.

**[Nuclei](https://github.com/projectdiscovery/nuclei)** — MIT · Go single binary · 30.8k★ · since 2020
Template-driven scanner for HTTP, DNS, TCP and other protocols, detecting known CVEs, misconfigurations, and exposed panels from a large community template library.
> `MAJOR` — The engine has no network dependency beyond the target. Templates are normally pulled live, but the pack is a plain YAML tree: clone once on a connected host and point at it with `-t <local-dir>`, or mirror the git repo.

**[Vuls](https://github.com/future-architect/vuls)** — GPL-3.0 · Go single binary, agentless over SSH · 12.2k★ · since 2016
Agentless vulnerability scanner for Linux and FreeBSD hosts, containers, and language dependencies, matching installed packages against CVE and OVAL data. The agentless design fits patch workflows well.
> `CONDITIONAL` — The scanner only touches its targets, but its databases (NVD, distro OVAL) are built by separate fetch tools that need internet. Those are plain SQLite files: build on a connected host, copy in, and it runs fully offline. The WordPress lookup needs a paid API key and won't work offline.

## Hardening & compliance

**[OpenSCAP](https://github.com/OpenSCAP/openscap)** — LGPL-2.1 · C library + `oscap` CLI · 1.8k★ · since 2008
The reference SCAP implementation and the certified engine behind most open-source compliance tooling. Evaluates a system or image against a hardening baseline and can remediate findings.
> `FULL` — Evaluates against a locally stored data-stream file. **There is no network call in the scan path at all.** The only "feed" is copying updated content in occasionally.

**[ComplianceAsCode/content](https://github.com/ComplianceAsCode/content)** — BSD-3-Clause · XML/Bash/Ansible content · 2.8k★ · since 2011
Generates the SCAP data streams and remediation content that OpenSCAP consumes, covering CIS Benchmarks, STIG, ANSSI, and PCI-DSS profiles. This is the actual benchmark content most open-source hardening tooling is built on.
> `FULL` — Static content with zero runtime component. Pull a new release tarball onto the network occasionally to pick up updated profiles.

**[Lynis](https://github.com/CISOfy/lynis)** — GPL-3.0 · Shell script, no daemon · 16.2k★ · since 2007
Security auditing and hardening tool for Linux, macOS and BSD, producing a scored report against CIS and ISO 27001 style checks plus concrete suggestions. One script, zero install friction.
> `FULL` — Every test runs against local system state. The optional update check against cisofy.com can simply be left unrun.

## Exploitation frameworks

**[Metasploit Framework](https://github.com/rapid7/metasploit-framework)** — BSD-3-Clause style · Ruby · 38.9k★ · since 2003
The standard exploitation framework: exploit development, a large curated module library, payload generation, and post-exploitation via Meterpreter. Unlike Metasploit Pro, the Framework is the actual product, not a demo of a paid one.
> `MAJOR` — Console, modules, Meterpreter, and the database all operate against targets with no internet. Only `msfupdate` and optional module metadata lookups need connectivity, servable from an internal git or apt mirror.

**[Sliver](https://github.com/BishopFox/sliver)** — GPL-3.0 · Go single binary · 11.7k★ · since 2019
Cross-platform adversary-emulation C2 supporting mTLS, WireGuard, HTTP(S) and DNS transports, with dynamic code generation and staged or stageless implants. The modern open alternative to commercial C2.
> `FULL` — Server, console, and implants are self-hosted binaries entirely under operator control. No vendor backend; the update checker is skippable.

**[Empire (BC-Security)](https://github.com/BC-SECURITY/Empire)** — BSD-3-Clause · Python 3 + Starkiller web GUI · 5.3k★ · since 2015
Post-exploitation and C2 with PowerShell, Python, C#, IronPython and Go agents, ATT&CK-mapped modules, and a web GUI. Multi-language agent support fills a distinct niche next to Sliver.
> `MAJOR` — Server and listeners are fully self-hosted at runtime. The setup script pulls OS and Python packages plus payload dependencies at install time, so those need pre-mirroring (pip cache, apt mirror, vendored binaries) for a true offline build.

## Network discovery

**[Nmap](https://github.com/nmap/nmap)** — NPSL (modified GPLv2; free for end-user use, commercial redistribution needs a license) · C++/Lua · 13.4k★ · since 1997
The foundational network discovery and port scanner, with OS and service fingerprinting and the Lua NSE scripting engine.
> `FULL` — Runs entirely locally against network targets. NSE scripts and fingerprint databases ship with the install and only need periodic manual refresh.

**[Masscan](https://github.com/robertdavidgraham/masscan)** — AGPL-3.0 · C single binary · 26k★ · since 2013
Asynchronous port scanner with its own TCP/IP stack, capable of millions of packets per second from one host. The tool for large-range sweeps where Nmap is too slow.
> `FULL` — Zero external dependencies of any kind. Nothing to sync or mirror.

## Credential auditing

**[Hashcat](https://github.com/hashcat/hashcat)** — MIT · C single binary, CPU/OpenCL/CUDA · 26.6k★ · since 2009
GPU-accelerated password recovery supporting 300+ hash and cipher types across dictionary, mask, rule-based and combinator attacks. The fastest widely used open cracker.
> `FULL` — Runs on local hardware against local hash files and wordlists. By construction it has no way to require network access.

**[John the Ripper (Jumbo)](https://github.com/openwall/john)** — GPL-2.0-or-later · C · 13.5k★ · since 1996
Broad-format offline cracker covering hundreds of hash and cipher types including NTLM, SSH keys, and encrypted archives. Complements Hashcat with the widest single-tool format coverage.
> `FULL` — Offline cracking against local files, CPU/GPU/FPGA accelerated. No network dependency.

**[THC-Hydra](https://github.com/vanhauser-thc/thc-hydra)** — AGPL-3.0 · C single binary · 12.2k★ · since 2001
Parallelized network login cracker supporting 50+ protocols (SSH, FTP, HTTP forms, SMB, databases). Still the standard protocol-broad online brute-force tool.
> `FULL` — Needs no external service of its own, only reachability to the targets being assessed.

**[NetExec](https://github.com/Pennyw0rth/NetExec)** — BSD-2-Clause · Python CLI · 5.8k★ · since 2015 (as CrackMapExec)
Swiss-army-knife AD tool for credential validation, spraying, enumeration, and lateral-movement checks across Windows environments. The actively maintained successor to CrackMapExec.
> `FULL` — Operates directly against hosts on the local network over SMB, WinRM, LDAP and MSSQL. Dependency install needs a mirrored pip index for a fully offline build.

## Wireless assessment

**[Aircrack-ng](https://github.com/aircrack-ng/aircrack-ng)** — GPL-2.0 · C CLI suite · 7.5k★ · since 2006
The foundational Wi-Fi assessment toolkit: monitoring and capture, deauthentication and replay, and WEP/WPA-PSK key cracking. Most other Wi-Fi tools wrap or depend on it.
> `FULL` — Captures and attacks locally via an adapter in monitor mode. No network service dependency of any kind.

**[Kismet](https://github.com/kismetwireless/kismet)** — GPL-2.0 · C++ + web UI · 2.2k★ · since 2001
Multi-protocol wireless detector, sniffer and IDS covering Wi-Fi, Bluetooth, SDR and Zigbee capture sources. Broader protocol coverage than Aircrack-ng for passive recon.
> `FULL` — Passive RF detection runs entirely against local capture hardware. Wigle upload integration is optional and not required.

**[Wifite2](https://github.com/derv82/wifite2)** — GPL-2.0 · Python script · 8.1k★ · since 2018
Automates the common Wi-Fi attack workflow — WPS Pixie-Dust, WPA handshake capture, PMKID capture, WEP — by driving Aircrack-ng, Reaver and Hashcat. Removes the hand-chaining where most wireless assessment time actually goes.
> `FULL` — Orchestrates local tools against in-range networks. No dependency beyond the wireless medium.

## Physical & hardware

**[Proxmark3 (Iceman fork)](https://github.com/RfidResearchGroup/proxmark3)** — GPL-3.0-or-later · C firmware + client · 6k★ · since 2016
The reference open-source RFID and NFC research platform, for reading, cloning and analyzing low- and high-frequency access-control cards. The standard tool for badge assessments.
> `FULL` — Interacts directly with tags via the Proxmark hardware. Firmware and client updates flash over USB from staged builds.

## Adversary emulation

**[Atomic Red Team](https://github.com/redcanaryco/atomic-red-team)** — MIT · YAML tests + PowerShell/Python executor · 12.4k★ · since 2017
A library of small portable test cases mapped to ATT&CK techniques, used to validate detection coverage technique by technique. The standard lightweight purple-team library.
> `MAJOR` — Once cloned, tests execute entirely on the local host. Only pulling updates needs GitHub, and the whole repo mirrors cleanly onto an internal git server.

**[MITRE Caldera](https://github.com/mitre/caldera)** — Apache-2.0 · Python server + web UI · 7.2k★ · since 2017
The most complete open-source ATT&CK-driven adversary emulation platform, running a C2 server that executes chained TTPs against target agents.
> `CONDITIONAL` — There is a **documented offline install**: clone with submodules and download pip dependencies on a connected machine, copy the tree across, install with `pip install --no-index --find-links`. The default slim install and some plugins fetch payloads on demand, so use the full build and pre-stage plugin payloads.

## Active Directory

**[BloodHound Community Edition](https://github.com/SpecterOps/BloodHound)** — Apache-2.0 · Docker Compose (React + Go + PostgreSQL + Neo4j) · 3.3k★ · since 2016
Graph-based AD and Entra attack-path analysis, mapping privilege relationships to reveal viable routes to high-value targets. The Enterprise-only features are not needed for core graph analysis.
> `MAJOR` — Ingestion and analysis run against locally imported collector data with no cloud dependency. Container images must be pre-pulled to an internal registry or loaded from tarballs, since Compose otherwise pulls from Docker Hub.

**[ADRecon](https://github.com/sense-of-security/ADRecon)** — AGPL-3.0 · PowerShell script · 1.9k★ · since 2018
Enumerates and reports AD state — forest and domain structure, trusts, users, groups, GPOs, DNS zones, ACLs — for offline analysis. Complements BloodHound's graph with a plain report.
> `FULL` — Queries AD directly over LDAP or ADWS from a single script. Runs even from a non-domain-joined host inside the network.

## Offline OSINT

**[ExifTool](https://github.com/exiftool/exiftool)** — GPL-3.0 / Artistic · Perl CLI · 5k★ · since 2003
Extracts and edits embedded metadata — GPS coordinates, device and software fingerprints, authorship, timestamps — from images, documents and many other formats.
> `FULL` — Reads files already on disk. There is no network call anywhere in its operation, which is rare for OSINT tooling.

## Assessment reporting

**[Dradis Community Edition](https://github.com/dradis/dradis-ce)** — GPL-2.0 · Ruby on Rails · 840★ · since 2007
Collaborative platform for aggregating tool output (Nmap, Burp, Nessus XML/JSON) and findings into a structured, exportable report. A usable free reporting hub, not a crippled demo. *(Pro adds report-generation automation, workflow, and client communication.)*
> `FULL` — Self-hosted, imports tool output by local upload. No external service call in normal operation.

---

# AppSec, Supply Chain & Cloud

## CI/CD policy gating

**[Open Policy Agent](https://github.com/open-policy-agent/opa)** — Apache-2.0 · Go single binary · 12.2k★ · since 2015
General-purpose policy engine that gates CI/CD pipelines, Kubernetes admission, Terraform plans and API authorization against declarative Rego rules. The engine underneath Conftest, Gatekeeper, and most policy-as-code gating.
> `FULL` — Policy evaluation is entirely local. The Rego Playground and remote bundle-server pull are opt-in and can point at an internal mirror or load from disk.

## SAST & DAST

**[Opengrep](https://github.com/opengrep/opengrep)** — LGPL-2.1 · OCaml, self-contained CLI · 3.0k★ · since 2024
Static analysis engine using Semgrep's YAML rule syntax, forked specifically because Semgrep Inc. moved cross-file taint analysis behind a paid tier. Governed by a 10+ vendor consortium so it can't be relicensed by one company again.
> `FULL` — Ships all languages and analyses including taint and interprocedural in the open build. Community rules clone once and load with `--config /local/path`; no registry phone-home for any feature.

**[OWASP ZAP](https://github.com/zaproxy/zaproxy)** — Apache-2.0 · Java desktop / headless / Docker · 15.7k★ · since 2010
The only actively maintained fully open-source DAST scanner with both a desktop UI and a CI-friendly headless mode. Intercepting proxy, active and passive scanning, spidering and fuzzing.
> `MAJOR` — Scanning and fuzzing run fully offline against the target. The add-on Marketplace needs internet to browse, but add-ons download on a connected machine and sideload via *File → Load Add-on File*.

## Software composition analysis

**[OWASP Dependency-Track](https://github.com/DependencyTrack/dependency-track)** — Apache-2.0 · Java, Docker · 4.1k★ · since 2013
Ingests CycloneDX and SPDX SBOMs and continuously correlates components against vulnerability and license data for an ongoing supply-chain risk view. The most widely deployed self-hosted SBOM platform.
> `CONDITIONAL` — The platform itself is fully self-hosted, but risk scoring depends on vulnerability intelligence (NVD, OSV, GitHub Advisories) that normally syncs online. Point the datasources at an internally mirrored feed. It is designed around pluggable mirrors rather than one SaaS backend.

## Secret scanning

**[Gitleaks](https://github.com/gitleaks/gitleaks)** — MIT · Go single binary · 28.9k★ · since 2018
Scans git history, working directories and CI pipelines for hardcoded secrets using pattern and entropy detection. The most widely adopted pure-offline secret scanner.
> `FULL` — Rules are embedded in the binary or loaded from a local TOML config. No telemetry, no external call in the scan path.

**[TruffleHog](https://github.com/trufflesecurity/trufflehog)** — AGPL-3.0 · Go single binary · 27.6k★ · since 2016
Scans source, binaries and cloud storage for 800+ secret types. The free CLI is the same detection engine the paid Enterprise product wraps with a dashboard — nothing in core scanning is paywalled.
> `MAJOR` — Detection across git history, filesystems, S3 and container images works offline. The headline live-credential-validation feature calls provider APIs by design and is the one thing that breaks; findings still surface with it disabled.

**[Kingfisher](https://github.com/mongodb/kingfisher)** — Apache-2.0 · Rust single binary · 1.2k★ · since 2025
Combines pattern matching with structural, language-aware parsing to find credentials in code and git history, with notably lower false-positive rates than pure-regex scanners.
> `MAJOR` — Core detection runs entirely offline against files or git history. Optional live validation needs internet; disable with `--no-validate`.

## Secrets management

**[OpenBao](https://github.com/openbao/openbao)** — MPL-2.0 · Go single binary / server · 7.2k★ · since 2023
Centralized secrets storage, dynamic credential generation, and encryption as a service, API- and CLI-compatible with HashiCorp Vault. The Linux Foundation-governed continuation after Vault moved to a non-OSI license.
> `FULL` — Unseal, storage backends (file, Raft, Consul) and auth methods all run inside the deployment boundary. No external calls required.

## SBOM & supply chain

**[Syft](https://github.com/anchore/syft)** — Apache-2.0 · Go single binary · 9.5k★ · since 2020
Generates CycloneDX or SPDX SBOMs by statically inspecting a filesystem, container image or archive. The most widely used standalone SBOM generator.
> `FULL` — No network access is used to produce an SBOM.

**[Grype](https://github.com/anchore/grype)** — Apache-2.0 · Go single binary · 12.8k★ · since 2020
Matches SBOMs or container images against a vulnerability database to report known CVEs in dependencies. Pairs with Syft as the standard generate-then-scan pipeline.
> `MAJOR` — Matching runs fully offline once the DB is local. **Documented air-gap support:** point `GRYPE_DB_UPDATE_URL` at an internally hosted `listing.json` mirror instead of the public endpoint.

**[Cosign](https://github.com/sigstore/cosign)** — Apache-2.0 · Go single binary · 6.2k★ · since 2021
Signs and verifies container images, SBOMs and arbitrary artifacts. Unlike keyless-only signing services, it explicitly supports a purely offline key-based mode.
> `CONDITIONAL` — Default keyless signing needs the public Fulcio CA and Rekor log. Offline you either run a private Sigstore stack (both are self-hostable) or use key-based signing with `cosign sign --key`, which needs no external service. Verification works offline either way once the bundle is local.

## Containers & Kubernetes

**[Trivy](https://github.com/aquasecurity/trivy)** — Apache-2.0 · Go single binary · 37.6k★ · since 2019
One binary covering container image and filesystem vulnerabilities, misconfigurations (Docker, Kubernetes, Terraform, CloudFormation), secrets and licenses.
> `MAJOR` — **The strongest air-gap story of any scanner here.** The vulnerability DB, Java DB and misconfiguration checks bundle ship as OCI artifacts you mirror into a private registry, and the checks bundle is also embedded in the binary as a fallback. Once mirrored, no further internet; add `--offline-scan` to stop Maven Central lookups too.

**[kube-bench](https://github.com/aquasecurity/kube-bench)** — Apache-2.0 · Go single binary / K8s Job · 8.2k★ · since 2017
Runs the CIS Kubernetes Benchmark against control-plane and node configuration to flag hardening gaps. The standard purpose-built CIS runner for Kubernetes.
> `FULL` — Checks are bundled YAML evaluated against local component config. No network calls during a run.

**[Falco](https://github.com/falcosecurity/falco)** — Apache-2.0 · C++, eBPF/kernel module + daemon · 9.3k★ · since 2016
Runtime detection of anomalous behavior — unexpected process execution, file access, network activity — in containers and Kubernetes nodes via kernel instrumentation. CNCF graduated.
> `MAJOR` — Detection against local rules runs fully offline. The Falcosidekick output plugin needs connectivity but isn't required to alert. Pre-built eBPF probes for the specific kernel version must be mirrored in.

**[Kyverno](https://github.com/kyverno/kyverno)** — Apache-2.0 · Go, in-cluster controller · 8.1k★ · since 2019
Kubernetes-native policy engine that validates, mutates and generates resources at admission time using YAML policies rather than Rego.
> `FULL` — Policies are CRDs evaluated in-cluster via admission webhooks. No external service in the enforcement path. Optional image verification checks an OCI registry, which is fine when that registry is internal.

## Infrastructure as code

**[Checkov](https://github.com/bridgecrewio/checkov)** — Apache-2.0 · Python CLI / Docker · 9.0k★ · since 2019
Static analysis for Terraform, CloudFormation, Kubernetes, Helm, Ansible and Dockerfiles, with thousands of built-in checks. The broadest IaC framework coverage of any single open scanner.
> `MAJOR` — Core scanning runs fully offline against bundled checks. Only optional check-update fetching and the paid platform integration need internet, and neither is required.

## AWS security & automation

**[Prowler](https://github.com/prowler-cloud/prowler)** — Apache-2.0 · Python CLI · 14.7k★ · since 2016
Hundreds of security and compliance checks (CIS, NIST, PCI-DSS) against AWS, Azure, GCP and Kubernetes, producing local HTML/JSON/CSV reports. The most comprehensive open cloud posture scanner.
> `MAJOR` — Talks only to the AWS APIs of the partition it's pointed at, with no SaaS callback in the findings path. For isolated partitions beyond the defaults, endpoints must be configured manually. The paid Prowler Cloud SaaS is a separate product.

**[ScoutSuite](https://github.com/nccgroup/ScoutSuite)** — GPL-2.0 · Python CLI · 7.8k★ · since 2018
Multi-cloud posture auditing that pulls configuration and renders it as a browsable report highlighting misconfigurations.
> `MAJOR` — Collection uses provider APIs, then **all analysis and rendering happen offline against a self-contained static HTML report.** The output is itself an air-gap-friendly artifact you can carry out and review anywhere.

**[Cloud Custodian](https://github.com/cloud-custodian/cloud-custodian)** — Apache-2.0 · Python CLI / Lambda · 6.1k★ · since 2016
YAML policy-as-code engine that queries, filters and takes automated action — tag, notify, quarantine, delete — on resources violating rules. The one tool here built for automated remediation rather than just reporting.
> `MAJOR` — Evaluation and remediation run against the target account's own APIs, with explicit support for scoping to alternate partitions by region config. No SaaS in the execution path.

**[PMapper](https://github.com/nccgroup/PMapper)** — AGPL-3.0 · Python CLI · 1.6k★ · since 2018
Builds a graph of an AWS account's IAM users, roles and policies to identify privilege-escalation paths and analyze effective permissions. The standard purpose-built tool for AWS IAM graph analysis.
> `MAJOR` — Clean split between a one-time `pmapper graph create` API pull and everything after: path-finding and querying run entirely offline against the stored graph.

**[S3Scanner](https://github.com/sa7mon/S3Scanner)** — MIT · Go single binary · 3.2k★ · since 2017
Scans S3 and S3-compatible bucket names for anonymous read or write access, and can dump accessible contents.
> `MAJOR` — `--endpoint-url` and a custom provider config let it target a specific partition endpoint or an internal S3-compatible store such as MinIO, so it runs entirely inside a closed environment.

**[Automated Security Helper](https://github.com/awslabs/automated-security-helper)** — Apache-2.0 · Python CLI · 684★ · since 2022
Orchestration CLI that runs a curated set of open-source scanners — Bandit, Opengrep, detect-secrets, Checkov, cfn_nag, cdk-nag, Grype, Syft — over a codebase and consolidates the findings.
> `MAJOR` — **Documented offline mode:** build the offline container ahead of time with `ash --mode container --offline --no-run`, push to an internal registry, then run with `--offline --no-build`. Local mode otherwise pulls scanner versions on first run unless `ASH_OFFLINE=true`.

## PKI & certificates

**[step-ca](https://github.com/smallstep/certificates)** — Apache-2.0 · Go single binary + `step` CLI · 8.8k★ · since 2018
Private certificate authority and ACME server for issuing and renewing short-lived TLS and SSH certificates internally. The most complete open private-CA implementation.
> `FULL` — No phone-home or license check. The `step` CLI has an explicit `--offline` mode operating directly against local config, database and keys with no server running, and the two-tier offline-root design is built around air-gapped key custody rather than fighting it.

---

# Portable Offline Utilities

**[CyberChef](https://github.com/gchq/CyberChef)** — Apache-2.0 · Static HTML/JS · 35.7k★ · since 2016
The "cyber Swiss Army knife" for chained data operations — encoding, crypto, compression, extraction, regex, format conversion — via a drag-and-drop recipe UI.
> `FULL` — A static bundle with no server component and no external calls. Open `index.html` from disk on a machine with no network at all and every operation still works. The reference implementation of an offline security utility.

**[it-tools](https://github.com/CorentinTh/it-tools)** — GPL-3.0 · Vue static web app / Docker · 40.4k★ · since 2020
Self-hostable collection of ~80 everyday dev and ops utilities — converters, generators, formatters, calculators, regex tester, network calculators — in one app.
> `MAJOR` — The vast majority run entirely client-side. A handful that are network-dependent by definition (public IP lookup, MAC vendor lookup) return nothing offline, which is expected of those specific tools rather than a platform limit.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). The bar is the [inclusion criteria](#inclusion-criteria), and the air-gap verdict is the part that matters most: if you submit a tool, say precisely what breaks when it is unplugged.

Tools that were evaluated and cut, with reasons, are in [NOT-INCLUDED.md](NOT-INCLUDED.md). Read it before opening an issue asking why something is missing.

## License

[CC0 1.0 Universal](LICENSE). The list is public domain; the tools it points at carry their own licenses.
