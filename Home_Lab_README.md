# Home Lab — Infrastructure & Security Engineering

A self-hosted, network-segmented lab built and operated as functional infrastructure and as an ongoing engineering practice.

> **On detail:** platforms are named; addressing, topology, versions, hostnames, counts, and configuration specifics are deliberately withheld. Operational detail of a live environment doesn't belong in a public repository.

> **On status:** what is running, what is in progress, and what is planned are listed separately. Nothing appears as a capability unless it is actually operating.

---

## Purpose

Most home labs are a pile of services on a flat network. This one is built the way a small production environment would be: workloads separated by trust level, every placement decision justified and written down, and changes version-controlled rather than typed into a terminal and forgotten.

The point is to practice the full lifecycle - design, build, secure, automate, document - at a scale where every decision has to survive a real constraint.

---

## Running today

**Network segmentation**
Segmented VLAN architecture (802.1Q) spanning management, development, production, data, and untrusted zones. Routing, DNS, DHCP, and inter-zone policy managed centrally on a dedicated **pfSense** appliance, trunked to a managed switch.

Each workload zone enforces the same tiered ruleset: permit essential gateway services, block lateral movement to other internal zones, block access to the firewall's own management plane, then permit egress scoped to what that tier legitimately needs. Every workload zone was validated against a written positive/negative test matrix, with cross-zone exceptions added only from observed evidence rather than speculatively.

The management segment is deliberately exempt from that pattern and kept permissive - it carries the anti-lockout rule and the out-of-band administrative path. That exemption is a documented, deliberate decision rather than an oversight, and it is treated as out of scope for tightening.

**Virtualization**
**Proxmox VE** hosting isolated Ubuntu Server guests distributed across trust zones, each with static addressing and internal DNS resolution. Memory ballooning tuned per workload, with a documented hard exclusion for the workload whose bundled database cannot tolerate host memory reclaim. Scheduled image-level backups cover part of the estate; remaining coverage gaps are tracked as known issues rather than left undiscovered.

**Self-hosted Git**
**GitLab** as the source of truth for code, configuration, and documentation, running in its own isolated data zone with multiple active repositories.

**Automation and services**
A scheduled automation pipeline built on **Docker**-hosted **n8n**, integrated with a self-hosted feed aggregator over its API - classifying content, updating upstream state, and writing output through a receiver endpoint on a separate host. The split across two hosts is deliberate, working around a documented sandbox limitation in the automation platform that blocks direct filesystem writes.

**Operational tooling**
Purpose-built **Bash** tooling for host maintenance, written to a consistent discipline: read-only preflight checks, hard invariant gates that refuse to apply rather than half-complete, post-change verification that proves the intended end state instead of assuming it, and backups taken before anything is touched so a rollback path exists in advance.

**Documentation**
A maintained, version-controlled documentation set: architecture, standing decisions, per-service runbooks, incident write-ups with root-cause analysis, and a full change history - written to be reproducible by someone else.

---

## In progress

Active focus is the Git and delivery workflow: **GitLab** as the internal source of truth, public mirroring to **GitHub**, and the development-to-production release path.

**CI/CD** - **GitLab CI/CD** with a pipeline definition authored and secret detection configured. A dedicated runner host is the gating dependency; no pipeline has executed yet.

**Deployment automation** - a pull-based release model is designed and documented: production pulls from Git rather than being pushed to, so no inbound path into the production zone is required. Atomic releases with rollback on health-check failure. Designed, not yet built.

**Configuration management** - **Ansible** controller running with inventory defined. Playbook coverage and a baseline hardening pass across hosts are the current work.

---

## Planned

- Security monitoring and detection engineering (**Wazuh**) - designed and planned; **deferred on current resource constraints**, to be sized to run within the platform's memory ceiling rather than assuming headroom
- Observability - metrics and availability monitoring across hosts; planned, **deferred on the same resource constraints**
- Self-hosted document management with OCR and full-text search, staged through development before promotion
- Notification transport - evaluated, documented, and implementation deliberately **held** pending further platform maturity

---

## How it's run

**Documentation-first.** Every architectural decision, constraint, and standing policy is written down and version-controlled alongside the infrastructure it describes - including the decisions *not* taken, and why.

**Disable, never delete.** Superseded configuration is disabled and annotated rather than removed. One-step rollback, and the history stays readable.

**Confirm before execute.** Live changes are proposed, reviewed, and explicitly confirmed before execution, with verification steps defined in advance and a rollback path identified before anything is touched.

**Verify, don't assume.** Diagnosis works outside-in, layer by layer. Claims are checked against the running system rather than accepted from documentation - including correcting the documentation when it has drifted from reality.

**Automation fails safe.** Scripts validate their own preconditions and refuse to run rather than half-completing. Nothing mutates until the operation is proven able to succeed.

**Constraints are design inputs.** The platform is deliberately modest. Every workload placement is justified against real limits rather than solved by adding hardware.

---

## Technology

**Working with:** Proxmox VE · KVM/QEMU · pfSense · VLAN segmentation (802.1Q) · firewall policy design · network isolation · Linux (Ubuntu Server) · GitLab · Git · Docker · n8n · Bash · Ansible · nginx · DNS · DHCP

**Building toward:** GitLab CI/CD · secret detection · pipeline security · Wazuh · SIEM and detection engineering · observability

---

## About

Built and maintained by a security practitioner focused on infrastructure, network segmentation, and offensive security, with a growing interest in AI security.

Contact details are on the profile associated with this account.
