# awesome-airgap

> Security tooling that survives the sneakernet.

A curated list of **116 open-source security tools that work with zero internet access.**

Every other security tool list assumes you can reach the internet. Plenty of teams can't. Disconnected labs, isolated OT and ICS networks, forensic workstations, regulated enclaves, offline build farms, incident response on a network you've just pulled the plug on — in all of them the constraint is the same, and most tooling quietly assumes it away.

So the question this list answers for every entry is not "is this a good tool?" It is **"what exactly breaks when you unplug it, and what does the upstream mirror have to carry so it doesn't?"**

Nothing here is a demo of a paid product. Nothing here phones home to a vendor to function.

Tools are listed at their canonical home, which is not always GitHub. Wireshark, GnuPG, OpenSSH and others develop elsewhere, and an earlier revision of this list missed them because its verification method assumed a GitHub API call. That is recorded here rather than quietly fixed.

---

## Inclusion criteria

A tool earns a slot only if it clears all of these:

1. **Open source and free in major or full capacity.** Open-core is allowed only where the free tier is genuinely useful at real scale. Where something is paywalled, the entry says so. Crippled free tiers are rejected and listed in [NOT-INCLUDED.md](NOT-INCLUDED.md) with the reason.
2. **Deployable air-gapped.** The tool itself gets zero internet access and must still work in major or full capacity. An upstream mirror may carry updates inward — signature packs, vulnerability databases, rule sets, container images. A cloud control plane, a mandatory license check, a SaaS backend, or online-only activation is disqualifying.
3. **A real, established project.** Not brand new, and with enough of a track record that someone other than the author depends on it. On GitHub that means more than 10 stars and preferably more than two contributors. Off GitHub, stars are the wrong metric: inclusion in Debian, Fedora or Alpine, a tagged release history spanning years, or a documented CVE-handling process all count for more.

Portable single binaries (Go, Rust, C) and static offline web apps are actively preferred. Splunk apps and add-ons are welcome where fully free.

## Air-gap ratings

| Rating | Meaning |
|---|---|
| `FULL` | Works completely offline. Nothing to mirror beyond the software itself. |
| `MAJOR` | Core function works offline. One or more secondary features need a mirrored feed or must be disabled. The entry names which. |
| `CONDITIONAL` | Works offline only after a specific setup step — staging a database, pre-bundling dependencies, or standing up a private backend. The entry names the step. |

Star counts, licenses and maturity signals were verified on **2026-08-25** against the GitHub API, the GitLab API, Debian package metadata, and project release pages, depending on where each tool actually lives.

---

## Contents

- [Detection & Response](#detection--response)
  - [SIEM & log pipelines](#siem--log-pipelines) · [SOC & NOC monitoring](#soc--noc-monitoring) · [EDR & host agents](#edr--host-agents) · [Incident response & case management](#incident-response--case-management) · [Digital forensics](#digital-forensics) · [Malware analysis & reverse engineering](#malware-analysis--reverse-engineering) · [Threat intelligence](#threat-intelligence) · [Network security monitoring](#network-security-monitoring) · [Detection engineering](#detection-engineering) · [Honeypots & deception](#honeypots--deception)
- [Offense & Assessment](#offense--assessment)
  - [Vulnerability scanning & management](#vulnerability-scanning--management) · [Hardening & compliance](#hardening--compliance) · [Exploitation frameworks](#exploitation-frameworks) · [Network discovery](#network-discovery) · [Credential auditing](#credential-auditing) · [Wireless assessment](#wireless-assessment) · [Physical & hardware](#physical--hardware) · [Adversary emulation](#adversary-emulation) · [Active Directory](#active-directory) · [Offline OSINT](#offline-osint) · [Assessment reporting](#assessment-reporting)
- [AppSec, Supply Chain & Cloud](#appsec-supply-chain--cloud)
  - [CI/CD policy gating](#cicd-policy-gating) · [SAST & DAST](#sast--dast) · [Software composition analysis](#software-composition-analysis) · [Secret scanning](#secret-scanning) · [Secrets management](#secrets-management) · [Password & credential managers](#password--credential-managers) · [SBOM & supply chain](#sbom--supply-chain) · [Containers & Kubernetes](#containers--kubernetes) · [Infrastructure as code](#infrastructure-as-code) · [AWS security & automation](#aws-security--automation) · [PKI & certificates](#pki--certificates)
- [Network & Host Infrastructure](#network--host-infrastructure)
  - [DNS](#dns) · [VPN & encrypted transport](#vpn--encrypted-transport) · [Host firewalling](#host-firewalling) · [Media sanitization](#media-sanitization)
- [Splunk](#splunk)
  - [Detection content & threat hunting](#detection-content--threat-hunting) · [Data onboarding & add-ons](#data-onboarding--add-ons) · [Deployment & administration](#deployment--administration) · [Development & testing tooling](#development--testing-tooling)
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

**[Samhain](https://www.la-samhna.de/samhain/)** — GPL-2.0 · C, standalone or client/server · in Debian main · since 1999
File integrity monitoring and host intrusion detection with centralized logging across a fleet, plus rootkit and hidden-process detection. Twenty-seven years of releases and still shipping: version 4.5.3 landed October 2025 with a detached GPG signature.
> `FULL` — Standalone mode needs nothing external. Client/server mode needs monitored hosts to reach the central log server, which lives on the isolated network too.

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

**[Wireshark](https://gitlab.com/wireshark/wireshark)** — GPL-2.0-or-later · C/C++ desktop app + `tshark` CLI · 1.6k★ GitLab, in Debian main · since 1998
The reference deep packet analyzer and protocol dissector, with a headless twin in `tshark` for capture pipelines and scripted triage. Nothing free comes close on protocol coverage. Its canonical repository has never been on GitHub, which is the only reason it was missing from the first revision of this list.
> `FULL` — Capture and dissection are entirely local, against a live NIC or a `.pcap`/`.pcapng` on disk. The three features that touch the network (the update check, MaxMind GeoIP lookup, and OUI vendor-database refresh) are optional and file-backed; GeoIP and OUI are plain data files a mirror carries in.

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

**[Exploit-DB](https://gitlab.com/exploit-database/exploitdb)** — GPL-2.0-or-later · Perl/shell (`searchsploit`) + flat-file archive · 254★ GitLab, upstream for Kali's `exploitdb` package · GitLab home since 2022
The local exploit archive behind `searchsploit`: exploits, shellcode and Google Hacking Database entries, full-text searchable against a copy on disk. The list had no exploit-reference entry at all before this.
> `FULL` — `searchsploit` queries a locally cloned archive with no network calls. The exploit-db.com web front end is irrelevant to the CLI.

**[debsecan](https://salsa.debian.org/debian/debsecan)** — GPL-2.0-or-later · Python · Debian's own security-audit tool, in Debian main · since 2005
Compares the packages actually installed on a Debian or derivative host against the Debian Security Bug Tracker and reports which have open vulnerabilities. Debian-native where Vuls is NVD- and OVAL-driven, so the two answer different questions on the same host.
> `CONDITIONAL` — The scan itself is local. Its vulnerability data comes from the Debian Security Tracker, normally fetched by `debsecan --update-cache`; that is a flat file, so stage it from a connected host on whatever cadence your risk tolerance allows.

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

**[GnuPG](https://gnupg.org/)** — GPL-3.0-or-later · C (`gpg`, `gpg-agent`, `gpgsm`) · in Debian main · since 1997
The reference OpenPGP implementation, and the substrate under most offline signing and encryption workflows: detached signatures on transfer media, encrypted archives crossing the gap, offline master-key custody with subkeys on a smartcard. Canonical development is at `git.gnupg.org`.
> `FULL` — Needs the binary and a local keyring, nothing else. Keyservers are optional; keys move as files, which is exactly the model an isolated network already uses.

**[pass](https://www.passwordstore.org/)** — GPL-2.0-or-later · Shell over GnuPG and git · in Debian main · since 2012
A password manager small enough to audit in an afternoon: each secret is a GPG-encrypted file in a directory tree, versioned with git. No database, no daemon, no server. Where OpenBao is the team's secret infrastructure, this is what an individual operator carries.
> `FULL` — GnuPG and git are the only dependencies. Syncing the git store to another host is optional and is itself the sneakernet transport.

## Password & credential managers

A vault that syncs through a vendor's cloud is the normal shape of this category and the one thing an isolated network cannot use. Everything below either keeps the vault as a local file or runs the sync server inside your own boundary.

**[KeePassXC](https://github.com/keepassxreboot/keepassxc)** — GPL-2.0 or GPL-3.0 · C++ desktop (Linux, macOS, Windows) · 28.5k★ · since 2016
The reference offline password manager: a single encrypted KDBX file, AES-256 or ChaCha20, with TOTP, SSH agent integration, hardware key support via YubiKey challenge-response, and a CLI (`keepassxc-cli`) for scripted use. The community fork that overtook the original KeePassX, and the default answer for credential storage on a disconnected workstation.
> `FULL` — The vault is a file on disk. Two features reach the network and both are off by default: the HIBP breach check and website favicon download. Browser integration talks to a local socket, not the internet.

**[KeePassDX](https://github.com/Kunzisoft/KeePassDX)** — GPL-3.0 · Android · 7.2k★ · since 2017
Opens the same KDBX vault on Android, with biometric unlock, hardware key support and autofill. The realistic companion when the vault has to travel to a phone that is itself off the network.
> `FULL` — Reads and writes a local KDBX file. No account, no sync service, no telemetry.

**[KeeWeb](https://github.com/keeweb/keeweb)** — MIT · Static web app / Electron desktop · 13k★ · since 2015
A KDBX-compatible manager that runs as a plain static web app, so it can be served from an internal web server or opened straight from disk with no install. Useful where deploying a desktop binary is the harder problem, which on a locked-down build is often.
> `FULL` — Self-host the static bundle or run the desktop build. Cloud storage backends (Dropbox, OneDrive, Google Drive) are optional integrations, unreachable and unnecessary offline.

**[Vaultwarden](https://github.com/dani-garcia/vaultwarden)** — AGPL-3.0 · Rust single binary / Docker · 66.2k★ · since 2018
Lightweight reimplementation of the Bitwarden server API in Rust, compatible with all official Bitwarden clients and running comfortably on hardware the official stack would not consider. It exists precisely because people wanted a self-hosted Bitwarden without the enterprise machinery, which makes it the better-fitting of the two here.
> `MAJOR` — Server, database and clients all run inside your boundary with no license check or activation. Icon fetching for vault entries calls out and should be disabled with `ICON_SERVICE` or an internal icon cache; nothing else reaches the network. Push notifications and HIBP checks are optional and stay off.

**[Bitwarden Server](https://github.com/bitwarden/server)** — AGPL-3.0 core · C# / .NET, Docker Compose · 19.9k★ · since 2015
The official server, self-hostable and genuinely AGPL for the core vault, sync, and organization features. Worth listing alongside Vaultwarden where the requirement is the vendor-supported implementation rather than the light one.
> `CONDITIONAL` — Self-hosting requires an installation ID and key issued from bitwarden.com, obtained once on a connected host and baked into the configuration before the deployment goes across. After that the vault operates within your boundary. Be aware that a set of enterprise-oriented modules under `bitwarden_license/` are source-available under the Bitwarden License rather than AGPL, so the free-and-open portion is the core, not the whole tree.

**[Passbolt](https://github.com/passbolt/passbolt_api)** — AGPL-3.0 · PHP (CakePHP) + PostgreSQL · 6.1k★ · since 2016
Team-oriented rather than individual: OpenPGP-based sharing with per-user keys, granular permissions on shared credentials, group management and an audit log. Where KeePassXC is one person's vault, this is the credential store a team shares and can prove who touched.
> `MAJOR` — Server, database and browser extension run entirely self-hosted, with OpenPGP encryption happening client-side. Email notifications need an internal SMTP relay or must be turned off, and the health check that pings passbolt.com for version currency should be disabled.

**[gopass](https://github.com/gopasspw/gopass)** — MIT · Go single binary · 7.1k★ · since 2017
A rewrite of `pass` in Go that keeps the GPG-encrypted-file-in-a-git-tree model but adds multi-store support, team secret sharing, and a usable Windows story that the shell original never had. Same threat model, fewer sharp edges.
> `FULL` — GPG and git only. A single static binary, which is the easiest possible artifact to carry across a gap.

**[Password Safe](https://github.com/pwsafe/pwsafe)** — Artistic-2.0 · C++ / Windows, Linux · 865★ · since 2015 on GitHub, project dating to 2002
The original, designed by Bruce Schneier's team and still actively maintained more than two decades later. A local encrypted database with a deliberately narrow feature set and no network code to audit, which is the point.
> `FULL` — Local database file. No sync service, no update telemetry, no online component of any kind.

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

# Network & Host Infrastructure

The plumbing an isolated network has to stand up for itself, in the cases where that plumbing is a security control rather than a convenience. An enclave with no internet still needs name resolution, encrypted transport between segments, host firewalling, and a defensible answer for wiping media on the way out.

The line drawn here: a security team owns and configures these. General-purpose components that merely happen to be security-adjacent are out, and the ones considered are named in [NOT-INCLUDED.md](NOT-INCLUDED.md).

## DNS

**[BIND 9](https://gitlab.isc.org/isc-projects/bind9)** — MPL-2.0 · C · in Debian main, forty years of production use · since 1984
The reference DNS implementation, covering both authoritative and recursive roles with full DNSSEC. Longer continuous deployment history than anything else on this page.
> `CONDITIONAL` — As an authoritative server for locally defined zones it needs nothing external. As a recursive resolver it needs either forwarders reachable inside the enclave or a locally staged root zone per RFC 7706, replacing the hints file. A naive resolver install will sit there querying root servers it cannot reach, so this is a configuration decision made up front, not a default.

**[Knot DNS](https://gitlab.nic.cz/knot/knot-dns)** — GPL-2.0-or-later · C · in Debian main, run by CZ.NIC for the .cz registry · since 2011
Authoritative-only DNS built for DNSSEC signing at scale by a national registry operator. A cleaner fit than BIND wherever recursion is not wanted, which in a segmented enclave is often.
> `FULL` — Authoritative by design, serving configured zones from local files with no upstream dependency. Signing keys are generated and held locally.

## VPN & encrypted transport

**[OpenSSH](https://www.openssh.com/)** — BSD-style · C, portable build · in Debian main, releases since 1999 · since 1995
The reference SSH implementation, and in an isolated network the default answer for remote administration, file transfer, port forwarding and jump hosts. Distributed as signed tarballs by the OpenBSD project; there is no GitHub canonical repo. Release 10.5p1 landed 11 August 2026.
> `FULL` — Fully offline for key and password auth. The certificate-authority workflow is self-hosted, so it changes nothing.

**[WireGuard tools](https://www.wireguard.com/)** — GPL-2.0 · C (`wg`, `wg-quick`) · in Debian main, kernel-mainlined 2020 · since 2016
Userspace configuration for the WireGuard VPN, whose kernel module has shipped in mainline Linux since 5.6. A few thousand lines of auditable code where the alternatives are a few hundred thousand, which matters when the review is yours to do.
> `FULL` — Kernel module plus `wg` and `wg-quick` is the entire stack. No external service at any point.

**[tinc](https://www.tinc-vpn.org/)** — GPL-2.0-or-later · C daemon · in Debian main · since 1998
Full-mesh VPN daemon that builds its own routing overlay instead of assuming a hub, which suits site-to-site links between isolated segments where no segment should be privileged. Version 1.0.37 released March 2026.
> `MAJOR` — Core mesh routing is fully offline. The optional invitation and auto-configuration flow assumes reachable peers, which is true of any VPN rather than a hidden dependency.

## Host firewalling

**[nftables / Netfilter](https://www.netfilter.org/)** — GPL-2.0 · C, kernel plus `nft` CLI · in Debian main, mainlined 2014 · since 2009
The Linux packet-filtering framework underneath host and network firewalling on effectively every distribution, and the successor to the iptables syntax. Canonical development is at `git.netfilter.org`.
> `FULL` — In the kernel and in every distro's base packages. Nothing to mirror beyond the distro repository you already mirror.

## Media sanitization

**[GNU coreutils `shred`](https://www.gnu.org/software/coreutils/)** — GPL-3.0-or-later · C, part of coreutils · in Debian main, part of every base install · since 2002
Overwrites a file or a whole block device in place. Listed not because it is obscure but because media sanitization is a real step in an air-gapped workflow and this list had no entry for it. Understand the limits before relying on it: `shred` assumes the filesystem overwrites in place, which journaling, copy-on-write and log-structured filesystems do not guarantee, and it cannot reach reallocated sectors or wear-levelled blocks on SSDs and flash. For those, use the drive's own secure-erase command or physical destruction, per your sanitization policy.
> `FULL` — A local file or block-device operation with no network dependency of any kind.

---

**[GnuTLS](https://gitlab.com/gnutls/gnutls)** — GPL-3.0-or-later (LGPL for the library) · C library + CLI · 325★ GitLab, in Debian main · since 2003
GNU's TLS, DTLS and X.509 implementation with PKCS#12 support, and the crypto backend for a large slice of free-software networking where OpenSSL is not wanted. `certtool` alone is a capable offline certificate workbench.
> `FULL` — A local library and CLI. The library originates no network calls of its own; connectivity is whatever the calling application does.

# Splunk

Splunk Enterprise itself is commercial software and is not on this list. What follows is the free, open-source ecosystem that runs on top of a Splunk instance you already have: detection content, add-ons, deployment automation, and development tooling.

Two things are specific to this section:

**Splunkbase is an internet service.** An isolated deployment means downloading the `.spl` or `.tgz` on a connected host and side-loading it by hand. Every entry below has real source on GitHub, which is what makes that possible and auditable. Several well-known Splunk apps are distributed only through Splunkbase with no published source, and they are listed in [NOT-INCLUDED.md](NOT-INCLUDED.md) rather than here.

**Install-time and run-time are different problems.** An app that pulls dependencies while you build it is fine, because the mirror can carry them. An app whose core search performs a live lookup against a vendor API is not, and most threat-intel enrichment add-ons are exactly that.

One negative result worth recording: the community custom-visualization space did not yield a single qualifying entry. Most viz apps ship without a license file, sit under ten stars, or have been abandoned for the better part of a decade. Details are in NOT-INCLUDED.md.

## Detection content & threat hunting

**[Splunk Security Content (ESCU)](https://github.com/splunk/security_content)** — Apache-2.0 · Splunk app (.spl) + YAML source · 1.7k★ · since 2018
Source repository behind Enterprise Security Content Update: hundreds of detections, analytic stories and playbooks mapped to MITRE ATT&CK, built with the open `contentctl` tooling. The single largest body of maintained, openly licensed SPL detection logic in existence.
> `MAJOR` — Existing detections, dashboards and the app itself run with zero internet access. New and updated content is the only thing needing an inward mirror of this repo or the packaged release.

**[Attack Data](https://github.com/splunk/attack_data)** — Apache-2.0 · Log sample datasets · 807★ · since 2020
Curated library of attack-simulation datasets used to validate ESCU detections and to test SPL against known-bad telemetry without generating it yourself. Static data with no runtime dependency of any kind.
> `FULL` — Clone once, use indefinitely. Nothing to mirror after the initial copy.

**[ThreatHunting](https://github.com/olafhartong/ThreatHunting)** — MIT · Splunk app (.spl) · 1.2k★ · since 2018
Over 130 saved searches built on Sysmon telemetry and mapped to ATT&CK, with a bundled Navigator coverage export. Unusually for a hunting pack this size, not one search depends on a hosted reputation or intel service.
> `FULL` — Nothing to mirror. Requires Sysmon data already flowing into an index; the author's separate `sysmon-modular` configuration is a convenience rather than a dependency. Last substantive push was 2023, but the searches target stable Sysmon event IDs.

**[Splunk4DFIR](https://github.com/mf1d3l/Splunk4DFIR)** — MIT · Docker Compose · 170★ · since 2024
Stands up Splunk with precompiled Sigma detections and triage dashboards for EVTX, Zeek, Suricata, syslog and forensic tool output from Hayabusa, MemProcFS and plaso, all read from local artifact folders. A forensics workstation in a compose file.
> `MAJOR` — Ingest and dashboards run offline once built. The `docker build` pulls the base Splunk image and Sigma pipelines, so build on a connected host and carry the image across, or pre-stage the dependencies in a local mirror.

## Data onboarding & add-ons

**[Splunk Connect for Syslog (SC4S)](https://github.com/splunk/splunk-connect-for-syslog)** — Apache-2.0 · syslog-ng container + Splunk TA · 180★ · since 2019
Containerized syslog-ng collector that normalizes syslog from hundreds of vendor devices, including firewalls, network gear and OT and ICS equipment, and forwards it to Splunk over HEC with CIM field mapping already done. The standard way to onboard third-party syslog without hunting down a TA per vendor.
> `FULL` — Runs as a local container stack. Nothing is contacted at runtime beyond the Splunk instance itself.

**[Stamus Networks App for Splunk](https://github.com/StamusNetworks/stamus_for_splunk)** — AGPL-3.0 · Splunk app (.spl) · 13★ · since 2020
Dashboards and CIM mappings for Suricata `eve.json` output. The vendor documents standalone use against plain Suricata sensors, without their commercial platform.
> `MAJOR` — Suricata EVE ingest and dashboards work fully offline. Pointing `local/ssp.conf` at a Stamus Central Server is an optional feature needing a reachable instance; leave it unconfigured and the app is unaffected.

**[TA-Sysmon-deploy](https://github.com/olafhartong/TA-Sysmon-deploy)** — MIT · Splunk deployment app · 32★ · since 2017
Distributes and version-pins the Sysmon binary and an ATT&CK-mapped configuration to endpoints through the Splunk Deployment Server, reinstalling automatically when the config changes. Solves Sysmon config drift across a fleet using infrastructure already present.
> `CONDITIONAL` — The app cannot bundle `Sysmon.exe`, because the Sysinternals license forbids redistribution, so fetch it once on a connected host and drop it in the app's `bin` folder before staging. After that, distribution runs entirely over the internal deployment-server channel. Last pushed 2020; the deployment-server interface it targets has not changed.

## Deployment & administration

**[docker-splunk](https://github.com/splunk/docker-splunk)** — Apache-2.0 · Dockerfiles / build tooling · 548★ · since 2018
Splunk's official container build tooling for Splunk Enterprise and the Universal Forwarder. The usual starting point for reproducible Splunk deployments.
> `MAJOR` — Builds and runs with zero internet access once the Splunk tarball and base OS image are mirrored inward. Nothing calls out at runtime. Accepting the Splunk license at first start is a configuration flag, not a network check. Note that GitHub reports no license for this repo; the Apache-2.0 text is at `docs/LICENSE.md`, confirmed by reading it.

**[splunk-ansible](https://github.com/splunk/splunk-ansible)** — Apache-2.0 · Ansible role · 402★ · since 2018
Splunk's official playbooks for standing up single-instance and clustered deployments, covering indexer clustering, search head clustering and licensing. Used internally by both docker-splunk and splunk-operator.
> `MAJOR` — Runs offline against hosts on the internal network. Only the Splunk software package needs staging locally instead of pulling from splunk.com. Same license caveat as docker-splunk: Apache-2.0 lives at `docs/LICENSE.md`.

**[splunk-operator](https://github.com/splunk/splunk-operator)** — Apache-2.0 · Go binary / Helm chart · 260★ · since 2019
Kubernetes operator managing Splunk Enterprise clusters as custom resources. Talks only to the Kubernetes API server and the cluster it manages.
> `MAJOR` — Operator and managed cluster run fully offline once the operator image and Splunk Enterprise image are mirrored into the cluster's registry.

**[CIM Vladiator](https://github.com/hire-vladimir/SA-cim_vladiator)** — Apache-2.0 · Splunk app (.spl) · 80★ · since 2015
Validates a TA's field extractions against the Common Information Model, surfacing missing required fields and out-of-spec values, with a browsable CIM field dictionary alongside. Replaces cross-referencing the CIM specification by hand, which is the actual alternative.
> `FULL` — A dashboard over locally indexed data and a bundled copy of the CIM specification. No external calls. Actively maintained after a decade.

**[Splunk Website Monitoring](https://github.com/LukeMurphey/splunk-website-monitoring)** — MIT · Splunk app (.spl) · 46★ · since 2013
Polls HTTP and HTTPS endpoints on a schedule and indexes availability, response time and content-match results, with alerting on outages. Points at internal services just as happily as external ones.
> `FULL` — The monitored endpoints only need to be reachable on the local network. The app itself never calls out.

**[Splunk Slideshow](https://github.com/LukeMurphey/splunk-slideshow)** — MIT · Splunk app (.spl) · 13★ · since 2014
Rotates through a configured list of dashboards on a timer for driving a SOC or NOC wall display, with no separate kiosk product involved.
> `FULL` — Drives Splunk's own dashboard URLs on a browser timer. Nothing external.

## Development & testing tooling

**[Attack Range](https://github.com/splunk/attack_range)** — Apache-2.0 · Python / Terraform · 2.5k★ · since 2019
Splunk's detection-engineering lab: builds a small Splunk instance plus attacker and victim hosts, then replays adversary techniques through Atomic Red Team or Caldera so detections can be written and validated against real telemetry.
> `CONDITIONAL` — Use the local provider, meaning VMware, libvirt or KVM, with pre-staged VM images. The cloud providers are the documented default and are not usable here, so this is a deliberate configuration choice rather than a drop-in.

**[Eventgen](https://github.com/splunk/eventgen)** — Apache-2.0 · Python · 396★ · since 2012
Configurable synthetic event generator producing realistic sample data from templates: auth logs, web logs, custom formats. Fills an index for testing dashboards, detections and add-ons when no live source exists.
> `FULL` — Generates from local config and sample files. Nothing to mirror. Last pushed 2023, and the config format has been stable for years.

**[pytest-splunk-addon](https://github.com/splunk/pytest-splunk-addon)** — Apache-2.0 · pytest plugin · 66★ · since 2020
Splunk's own pytest plugin for automated TA testing, covering CIM field validation and both index-time and search-time checks. This is what Splunk's internal add-on pipeline runs.
> `FULL` — Tests execute against a local Splunk instance. No external service involved.

**[UCC Generator](https://github.com/splunk/addonfactory-ucc-generator)** — Apache-2.0 · Python CLI · 85★ · since 2020
Scaffolds a complete TA with a standard configuration UI, schema and REST handlers from one declarative spec, removing most of the boilerplate from building an add-on. Splunk builds many of its own TAs with it.
> `FULL` — Generates source locally from a local spec. The resulting add-on is side-loadable like any other.

**[Splunk SDK for Python](https://github.com/splunk/splunk-sdk-python)** — Apache-2.0 · Python library · 741★ · since 2011
The official client library for the Splunk REST and search API, underpinning custom apps, modular inputs and automation scripts.
> `FULL` — Talks only to the Splunk instance it is pointed at. No telemetry, no phone-home.

**[MLTK Container (DSDL)](https://github.com/splunk/splunk-mltk-container-docker)** — Apache-2.0 · Docker container · 64★ · since 2019
The open-source container backing Splunk's Data Science and Deep Learning app, providing a local Jupyter, TensorFlow and PyTorch runtime so model training and inference happen on your hardware instead of a hosted ML service.
> `CONDITIONAL` — Build the image once with Python packages mirrored inward, or carry a prebuilt image across. Training and inference then run entirely locally with no external API calls.

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
