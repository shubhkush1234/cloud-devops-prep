Here is the detailed, scannable, and master-level summary of the training session, including additional industry resources and structural insights as a DevOps Architect.
639 p2 azure landing zone 23.05.2026_21.06.38_REC
---

## 📌 To-the-Point Summary

This module focuses on the end-to-end framework for designing and executing a cloud migration. It contrasts **Greenfield** vs. **Brownfield** architectures and defines the critical pillars of an enterprise-ready **Cloud Landing Zone**. The strategy covers the migration path from on-premises datacenters down to continuous delivery architectures, highlighting system architecture planning over standard coding tasks.

---

## 📋 Interview Questions Asked by the Trainer

1. **Who are the Business Owners/Application Owners for the workload?**
2. **What is the Business Criticality of the architecture under review?**
3. **What is the Downtime Tolerance (Zero Downtime vs. Acceptable Window)?**
4. **What are the technical dependencies of the applications (e.g., Code Stack, Runtime Versions)?**
5. **What are the infrastructure requirements (e.g., Hyper-V vs. Bare Metal vs. VMware)?**
6. **What is the current Network Topology and IP allocation strategy?**
7. **What types of databases (SQL, NoSQL, etc.) are in use, and what are their specifications?**
8. **What are the RTO (Recovery Time Objective) and RPO (Recovery Point Objective) targets?**
9. **What security and compliance frameworks must the Landing Zone support?**

---

## 🔍 Top 10 L2/L3 Interview Questions (With Verified Answers & Diagrams)

### Q1. Explain the Hub-and-Spoke Network Topology in Azure and why it's used for Landing Zones.

* **Asked in Multiple Interviews Before (Amazon, Microsoft, Capgemini)** `[IMPORTANT]`

**Answer:**
The Hub-and-Spoke topology centralizes shared infrastructure (the "Hub")—such as Virtual Network Appliances, Firewalls, ExpressRoute Gateways, and shared services (Log Analytics)—while isolating separate business workloads into their own virtual networks ("Spokes"). This separation ensures perimeter-level security, centralized logging, governance, and optimized data center costs.

```
                  [ ON-PREM DATA CENTER ]
                            │
                    ( ExpressRoute / VPN )
                            │
              ┌─────────────▼─────────────┐
              │         HUB VNET          │
              │  ┌─────────────────────┐  │
              │  │  Azure Firewall     │  │
              │  └─────────────────────┘  │
              └─────────────┬─────────────┘
                            │
         ┌──────────────────┴──────────────────┐
   (VNet Peering)                       (VNet Peering)
         │                                     │
┌────────▼────────┐                   ┌────────▼────────┐
│   SPOKE VNET 1  │                   │   SPOKE VNET 2  │
│  [Prod App]     │                   │  [Non-Prod App] │
└─────────────────┘                   └─────────────────┘

```

*Source: Internet*

---

### Q2. How do Azure Policies differ from Azure RBAC, and how do they function together inside a Landing Zone?

* **Asked in Multiple Interviews Before (Wipro, TCS, Cognizant)** `[IMPORTANT]`

**Answer:**

* **Azure RBAC (Role-Based Access Control):** Focuses on **user actions**—who has access and what actions they can perform (e.g., Contributor, Reader).
* **Azure Policy:** Focuses on **resource state and properties** during creation or updates (e.g., enforcing that only `Standard_D2s_v3` sizes can be launched, or ensuring logs are directed to a specific Log Analytics Workspace).

```
[ User Request ] ──► [ Azure RBAC: Checked? ] ──► [ Azure Policy: Checked? ] ──► [ Resource Created ]
                          (Who are you?)               (What are you doing?)

```

*Source: Internet*

---

### Q3. Define RTO and RPO and explain how they dictate the Disaster Recovery (DR) implementation.

* **Asked in Multiple Interviews Before (Adobe, Cisco, Oracle)** `[IMPORTANT]`

**Answer:**

* **RTO (Recovery Time Objective):** The maximum targeted duration of time between the failure of an app and its restoration back to service.
* **RPO (Recovery Point Objective):** The maximum targeted period in which data might be lost due to a major incident (data age since last backup).

```
   Data Backups Taken [ X ] ────► [ X ] ────► [ X ] ───────┐
                                                            ▼
Timeline: ───────────────────────────────────────────► [ CRASH ] ──────────────────► [ RESTORED ]
                                                      ◄────────►                  ◄─────────────►
                                                         RPO                              RTO
                                                   (Data Lost Age)                  (Downtime Window)

```

*Source: Internet*

---

### Q4. What is a "Síncope Convulsivo" (Convulsive Syncope) in the context of system failure terminology used during structural assessments?

**Answer:**
Metaphorically used by system architects, a convulsive syncope refers to a system-wide failure where a momentary drop in infrastructure power or networking capacity triggers a cascade of secondary failures (like a sudden power loss to the brain causing muscle twitches). The underlying system components are healthy, but transient instability causes the architecture to behave as if it experienced a catastrophic breakdown.

```
[ Transient Resource Drop ] ──► [ False Positives / Alarms ] ──► [ Cascade Failure Actions ] 

```

*Source: Internet*

---

### Q5. Explain Greenfield vs. Brownfield Deployments in Cloud Infrastructure.

* **Asked in Multiple Interviews Before (Deloitte, Accenture)** `[IMPORTANT]`

**Answer:**

* **Greenfield:** A completely fresh infrastructure setup with zero legacy code or components to consider.
* **Brownfield:** A migration scenario where existing legacy infrastructure, active code dependencies, databases, and configuration settings must be transformed into a new architectural layout without affecting business runtime operations.

```
Greenfield:  [ Empty Ground ] ───────────────────────────────────────────────► [ Modern Landing Zone ]

Brownfield:   [ On-Prem Data Center ] ──► [ Refactor / Lift & Shift ] ────────► [ Modern Landing Zone ]

```

*Source: Internet*

---

### Q6. What is an Application Gateway with WAF (Web Application Firewall) and where does it sit in an enterprise cloud architecture?

* **Asked in Multiple Interviews Before (IBM, VMware)** `[IMPORTANT]`

**Answer:**
Azure Application Gateway is a layer-7 load balancer that handles HTTP/HTTPS traffic routing based on URLs, cookies, and headers. It features an integrated Web Application Firewall (WAF) to block layer-7 vulnerabilities (SQL injection, cross-site scripting) before traffic reaches internal application backend servers.

```
[ Public Web Traffic ] ──► [ App Gateway + WAF ] ──► [ Private Subnet ] ──► [ Backend VMs / AKS ]

```

*Source: Internet*

---

### Q7. Explain the importance of a Runbook/Operational Readiness Document.

**Answer:**
A Runbook is a prescriptive step-by-step procedural manual designed to guide operations teams through infrastructure execution, maintenance routines, failovers, updates, scale-down sequences, and active alert resolution without needing intervention from the core development architect.

```
[ System Alert Triggered ] ──► [ Operations Engineer ] ──► [ Follow Prescriptive Runbook Steps ] ──► [ Resolved ]

```

*Source: Internet*

---

### Q8. What is the role of an Azure Log Analytics Workspace in enterprise observability?

**Answer:**
A Log Analytics Workspace acts as a centralized repository that ingests, indexes, and queries telemetry, tracking logs, metrics, diagnostic registers, security audit trails, and events from VMs, app containers, databases, and load balancers inside a secure infrastructure ecosystem.

```
[ VM Logs ] ──────┐
[ AKS Metrics ] ──┼──► [ Centralized Log Analytics Workspace ] ──► [ KQL Queries / Monitoring Dashboards ]
[ Firewalls ] ────┘

```

*Source: Internet*

---

### Q9. How do Private Endpoints secure Azure PaaS Services?

* **Asked in Multiple Interviews Before (Infosys, HCL)** `[IMPORTANT]`

**Answer:**
A Private Endpoint creates a virtual network interface card (NIC) with a private IP address from your Spoke VNet subnet directly inside the target PaaS service (e.g., Azure SQL Database, Storage Accounts). This ensures all data transit remains strictly within the internal network fabric instead of traversing the public internet endpoint.

```
[ Private Spoke Subnet (VM) ] ──► [ Private IP Interface ] ──► [ Azure Private Link ] ──► [ Azure SQL DB ]

```

*Source: Internet*

---

### Q10. What is the purpose of Azure Bastion, and how does it replace traditional Jumpboxes?

**Answer:**
Azure Bastion provides secure, seamless RDP and SSH connectivity directly inside the Azure Portal over SSL (HTTPS), eliminating the need to expose a public IP address or manage a traditional Jumpbox VM vulnerable to brute-force port scanners.

```
[ User Browser ] ──► [ Azure Bastion (Port 443) ] ──► [ Target VM Internal IP (Port 3389/22) ]

```

*Source: Internet*

---

## 💡 Architect Tips & Intelligent Suggestions

> **From the Desk of a DevOps Architect (20+ Years Experience):**

1. **Avoid Code-First Bias:** Do not start writing Terraform configurations until the HLD (High-Level Design), LLD (Low-Level Design), and network CIDR map are finalized. Writing code before your architectural blueprints are complete leads to technical debt.
2. **Design with the End State in Mind:** Establish clear constraints early on. Define your RTO and RPO metrics *before* implementing high availability setups. If a customer cannot afford a multiple-region disaster recovery plan, do not spend time building it.
3. **Automate Governance early:** Treat governance as a core element of your build phase rather than an afterthought. Apply structural Azure Policies, standard resource tagging, and custom RBAC permissions across your management groups at day one. It is far more difficult to fix compliance gaps once multiple workloads are running in production.
