# System Environment Governance Guideline

## 1  Purpose

Establish a clear, repeatable framework for governing the health of all application environments—from Development to Production—by defining how issues are tracked, prioritized, owned, and resolved within agreed‑upon service‑level agreements (SLAs).

## 2  Scope

These guidelines apply to every team that deploys, supports, or consumes the following shared environments:

| Environment                                 | Code Line Purpose                                                                            | Abbrev.   |
| ------------------------------------------- | -------------------------------------------------------------------------------------------- | --------- |
| **Production**                              | Live customer‑facing workloads                                                               | **Prod**  |
| **Production – Parallel**                   | Blue/green or canary workloads; extension of Prod enabling parallel validation               | **PRODX** |
| **Extended Acceptance Test**                | Pre‑production integration/regression                                                        | **XAT**   |
| **Extended Acceptance Test – Experimental** | Extends XAT capacity; used for feature toggles, dark‑launch testing, and parallel validation | **XATX**  |
| **User Acceptance Test**                    | Business stakeholder validation                                                              | **UAT**   |
| **User Acceptance Test – Experimental**     | UAT with in‑flight schema/data changes                                                       | **UATX**  |
| **Quality Assurance**                       | Team‑owned functional tests                                                                  | **QA**    |

## 3  Governance Principles

1. **Client‑first reliability.** Any defect that threatens customers or regulated data is addressed ahead of all other work.
2. **Single system of record.** Every anomaly, bug, or degradation has a ticket in the chosen issue‑tracking tool.
3. **Transparency.** Dashboards surface real‑time health and SLA attainment to all stakeholders.
4. **Accountability with empathy.** Clear ownership prevents hand‑offs, but collaboration is encouraged across teams.
5. **Inspect & adapt.** Metrics drive retrospectives and continuous improvement.

## 4  Issue‑Tracking & Dashboard Setup

* **Tooling**: Use *Azure DevOps Boards* (primary) and integrate with *PagerDuty* for paging.
* **Mandatory ticket fields**: Environment, Priority, Service/Component, Severity, Detected by, Owner, Start Time, Resolution Time, Root‑Cause Category.
* **Dashboards** (Power BI or Grafana):

  * *Real‑time* SLA breach heat‑map
  * MTTA/MTTR trend lines per environment
  * Top recurring root‑cause categories
  * On‑call load (tickets per engineer)

## 5  Priority & SLA Matrix

| Environment | Priority | Clock Starts    | **Resolution SLA**                       |
| ----------- | -------- | --------------- | ---------------------------------------- |
| **Prod**    | **P1**   | Ticket creation | **4 hours**                              |
| **PRODX**   | **P1**   | Ticket creation | **4 hours**                              |
| **XAT**     | P2       | Ticket creation | **4 hours** (high‑priority issues only)  |
| **XATX**    | P2.5     | Ticket creation | **12 hours** (high‑priority issues only) |
| **UAT**     | P3       | Ticket creation | **12 hours**                             |
| **UATX**    | P3.5     | Ticket creation | **24 hours**                             |
| **QA**      | P4       | Ticket creation | **24 hours**                             |

> **Note:** “Clock starts” = timestamp when the issue is logged. SLAs measure *time‑to‑restore* or *workaround in place*—whichever occurs first.

## 6  Issue Ownership & Resolution Workflow

```mermaid
flowchart TD
    A[Issue Logged] --> B{Auto‑Triage Rules}
    B -- Valid --> C[Assign Owner]
    B -- Noise --> Z[Close as Monitoring Noise]
    C --> D[Acknowledge (≤15 min)]
    D --> E[Investigate]
    E -->|Root Cause Found| F[Fix/Workaround]
    F --> G[Verify & Close]
    G --> H[Post‑mortem (P1 only)]
```

### 6.1  Roles & Responsibilities

| Role                        | Primary Responsibilities                                |
| --------------------------- | ------------------------------------------------------- |
| **On‑Call Engineer**        | Triages, restores service, writes blameless post‑mortem |
| **Service/Component Owner** | Ensures sustainable fix, accepts preventive actions     |
| **Incident Commander**      | Coordinates multi‑team response for P1/P2               |
| **SRE Lead**                | Maintains runbooks, approves SLA changes                |
| **Product Owner**           | Communicates impact to stakeholders                     |

### 6.2  Escalation Path

1. On‑call engineer cannot resolve within **50 %** of remaining SLA → escalate to Service Owner.
2. **Prod P1** surpasses SLA → engage Incident Commander & Executive Sponsor.

## 7  Reporting & Continuous Improvement

* **Weekly SRE Review**: MTTR, SLA breaches, aging tickets, action‑item follow‑ups.
* **Monthly Reliability Scorecard**: Environment uptime %, incident counts, top three systemic issues.
* **Quarterly Post‑mortem Deep‑Dive**: Trend analysis + roadmap adjustments.

## 8  Compliance & Exceptions

Any deviation from these guidelines (e.g., scheduled downtime tests) must be:

1. Documented in the ticketing system **before execution**.
2. Approved by the SRE Lead and impacted Product Owners.

## 9  Glossary

| Term         | Definition                                                                                                        |
| ------------ | ----------------------------------------------------------------------------------------------------------------- |
| **SLA**      | Service‑Level Agreement—maximum acceptable time to restore or mitigate an incident.                               |
| **MTTA**     | Mean Time to Acknowledge.                                                                                         |
| **MTTR**     | Mean Time to Restore/Resolve.                                                                                     |
| **P1 – P4**  | Priority bands that determine resource allocation and SLA timers.                                                 |
| **XAT/XATX** | Extended Acceptance Test environments used for large‑scale, production‑like validation.                           |
| **PRODX**    | Parallel Production environment for blue/green deployments and shadow traffic; shares SLA and priority with Prod. |
