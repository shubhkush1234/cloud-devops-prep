# Azure DevOps & Trunk-Based Infrastructure Notes

---

## ## To-the-Point Summary

This session covers branching strategies, project management hierarchies within **Azure DevOps (ADO)**, and how organizations map their structures using **Azure Boards**. The trainer demonstrates the transition from **GitFlow to Trunk-Based Branching Strategies**, explicitly highlighting its benefits for infrastructure as code (Terraform). The lesson then dives deep into the administrative landscape of Azure DevOps, illustrating the differences between **Organization Settings** and **Project Settings**, along with setting up service hooks, artifact management, and project pipelines.

---

## ## Comprehensive Lecture Notes

### 1. Work Tracking & Agile Hierarchy (Azure Boards vs. Jira)

The trainer explained the core structures used for managing tasks within software teams using **Azure Boards**:

* **Epic**: The highest-level container representing a massive planning target (e.g., spanning a quarter or 6 months).
* **Feature**: Sourced directly under an Epic to deliver new functionalities to the product.
* **Issue / Bug**: Used to track problems or defects discovered in existing systems.
* **Tasks**: Formed under features or issues; these are atomic work items assigned directly to individual engineers.

> **Trainer Note (Azure Boards vs. Jira Hierarchy):**
> * **Azure Boards**: Epic $\rightarrow$ Feature / Issue $\rightarrow$ Tasks
> * **Jira**: Epic $\rightarrow$ Stories $\rightarrow$ Tasks
> 
> 

```
[Azure Boards Hierarchy]
       Epic
      /    \
  Feature   Issue
    |         |
  Tasks     Tasks

```

### 2. Branching Strategies: GitFlow vs. Trunk-Based Strategy

* **Trunk-Based Strategy**: Highly recommended and frequently used for **Terraform** environments.
* It utilizes exactly **one long-lived branch** (`main`).
* Engineers create short-lived **Feature Branches** branching off from `main`.
* Once work is verified, a **Pull Request (PR)** is raised, reviewed, and merged immediately back into `main`.

```
[Trunk-Based Workflow Diagram]
main ----------O-----------------------O----------> (Long-lived)
                \                     /
feature          O---[Commit]---O---PR

```

### 3. Evolution History of Azure DevOps

The trainer walked through the historical timeline of Microsoft's DevOps offerings:

* **2006 – 2013**: Team Foundation Server (TFS)
* **2013 – 2015**: Visual Studio Online
* **2015 – 2018**: Visual Studio Team Services (VSTS)
* **2018 – Present**: Azure DevOps (ADO)

---

### 4. Admin Architecture: Organization vs. Project Level Settings

Azure DevOps segregates configurations cleanly between global organizational management and isolated project environments:

| Organization Level Settings | Project Level Settings |
| --- | --- |
| **Global User & Group Rules** (Access levels: Basic, Stakeholder) | **Teams configuration** (Project-specific permissions) |
| **Billing Management** & Parallel Job Purchases | **Service Connections** (Links to cloud providers like Azure/AWS) |
| **Microsoft Entra ID Integration** (Directory Sync) | **Repositories & Pipelines** (Branch policies, build runs) |
| **Global Extensions** installation from Marketplace | **Service Hooks** (Integrations like Slack / MS Teams webhooks) |

---

## ## Interview Questions Discussed by Trainer

* **Q: What is the hierarchy of work items within Azure Boards, and how does it contrast with Jira?**
* *Trainer Explanation*: Azure Boards breaks milestones down into Epic $\rightarrow$ Feature $\rightarrow$ Tasks. In contrast, Jira uses Epic $\rightarrow$ Stories $\rightarrow$ Tasks.


* **Q: Why do we prefer Trunk-Based development over GitFlow for managing Infrastructure as Code (Terraform)?**
* *Trainer Explanation*: Infrastructure deployments require immediate state updates. Multiple long-lived environment branches cause state synchronization drift. Trunk-based development with short-lived feature branches prevents merge conflicts.



---

## ## Top 10 Technical Interview Questions & Answers

### Q1. What is Trunk-Based Development, and how does it compare to GitFlow?

* **Company Asked**: Amazon, Microsoft
* **Importance**: **Marked as Important** (Crucial for CI/CD metrics)

#### Answer

Trunk-Based Development is a version control management practice where developers merge small, frequent updates into a single core branch, typically called `main` or `trunk`. It avoids the long-lived branches seen in GitFlow (like separate `develop`, `release`, and `feature` branches), dramatically reducing merge conflict overheads and enabling pure Continuous Integration.

```
[Source: Internet - GitFlow vs Trunk-Based Comparison]
GitFlow:
main    ----------------------------------[Release]--->
develop      \---O-------O-------O-------/
feature           \--O--O/    \--O--O/

Trunk-Based:
main    ---------O---------------O-------O------------>
                  \             /       /
feature            O--[PR Merge]       O--[PR Merge]

```

---

### Q2. Explain the core work item hierarchy inside Azure Boards.

* **Company Asked**: Accenture, Capgemini
* **Source**: Video Session

#### Answer

Azure Boards maps business requirements into trackable milestones using a clean top-down engineering tree layout:

```
[Source: Internet - Work Item Map]
+-------------------------------------------------------+
|                       EPIC                            |
|             (Quarterly Business Target)               |
+-------------------------------------------------------+
                           |
          +----------------+----------------+
          |                                 |
+-------------------+             +-------------------+
|     FEATURE A     |             |     FEATURE B     |
| (Product Aspect)  |             | (Product Aspect)  |
+-------------------+             +-------------------+
          |                                 |
    +-----+-----+                     +-----+-----+
    |           |                     |           |
+-------+   +-------+             +-------+   +-------+
| TASK1 |   | TASK2 |             | TASK3 |   | TASK4 |
+-------+   +-------+             +-------+   +-------+

```

---

### Q3. What is the architectural difference between Organization-level Agent Pools and Project-level Agent Pools?

* **Company Asked**: Infosys, HCL
* **Source**: Video Session

#### Answer

An organization-level agent pool hosts shared compute agents accessible globally across all projects inside that specific organization directory. Project-level allocation relies on registering custom references targeting a shared pool or initializing isolated self-hosted pipeline containers unique to that specific project setup.

```
+-----------------------------------------------------------+
|                  ORGANIZATION AGENT POOL                  |
|                 [Shared Worker Node Farm]                 |
+-----------------------------------------------------------+
                     /                  \
                    /                    \
+-------------------------+        +-------------------------+
|        PROJECT A        |        |        PROJECT B        |
| [References Shared Pool]|        | [References Shared Pool]|
+-------------------------+        +-------------------------+

```

---

### Q4. What are Azure Artifacts, and what specific formats do they manage?

* **Company Asked**: TCS, Cognizant
* **Source**: Video Session

#### Answer

Azure Artifacts acts as an integrated, universal artifact repository extension within Azure DevOps. It allows teams to host, secure, and share universal build packages seamlessly. It natively supports **NuGet, npm, Maven, Python (pip), and Universal Packages**.

```
[Build System] ---> [Pipeline Run] ---> [Azure Artifacts Store]
                                                |
                                    +-----------+-----------+
                                    |     |     |     |     |
                                  NuGet  npm  Maven  pip Universal

```

---

### Q5. How do Service Hooks operate to push notifications from ADO to third-party tools like Microsoft Teams or Slack?

* **Company Asked**: Wipro, Deloitte
* **Importance**: **Marked as Important** (Common Operational Setup Query)

#### Answer

Service Hooks enable you to perform management tasks on external services when specific events happen within your Azure DevOps projects. ADO securely sends a JSON payload containing contextual event details (e.g., build completion, release trigger) directly to external destination endpoints via predefined webhooks.

```
+---------------+                    +--------------+                    +-----------------+
|  ADO Pipeline | --(Triggers Event)-> | Service Hook | --(HTTP POST JSON)-> | External Target |
|  Event Fired  |                    | Engine Core  |                    | (Slack/MS Teams)|
+---------------+                    +--------------+                    +-----------------+

```

---

### Q6. What is a Service Connection in Azure DevOps, and why is it required?

* **Company Asked**: IBM, Cisco
* **Source**: Internet

#### Answer

A Service Connection is a secure credential store managed by Azure DevOps to connect to external databases, remote endpoints, or cloud resource platforms (such as Microsoft Azure subscriptions, AWS environments, or Docker registries) without exposing plaintext security variables inside pipeline source codes.

```
[Azure DevOps Pipeline]
          |
   (Secure Token)
          v
+--------------------+
| Service Connection |
+--------------------+
          |
   (OIDC / SPN Authentication)
          v
[Target Infrastructure Platform (Azure / AWS)]

```

---

### Q7. Explain the fundamental stages of the Software Development Life Cycle (SDLC).

* **Company Asked**: Tech Mahindra
* **Source**: Video Session

#### Answer

The standard Software Development Life Cycle (SDLC) flows sequentially through discrete operational phases to ensure high-quality application releases:

```
[1. Planning] ---> [2. Analysis] ---> [3. Architecture/Design]
                                              |
[6. Maintenance] <--- [5. Testing] <--- [4. Implementation]

```

---

### Q8. Map the complete historical evolution of Microsoft's DevOps suite.

* **Company Asked**: Microsoft
* **Source**: Video Session

#### Answer

Microsoft's flagship deployment platform transitioned from a legacy, on-premises centralized server application tracking environment to a completely modern cloud-native ecosystem:

```
+--------------------------+
|  TFS (2006-2013 Era)     | Centralized Repository & Task Management
+--------------------------+
             |
             v
+--------------------------+
| Visual Studio Online     | Early cloud migration testing framework
+--------------------------+
             |
             v
+--------------------------+
|  VSTS (2015-2018 Era)    | Visual Studio Team Services Platform
+--------------------------+
             |
             v
+--------------------------+
| Azure DevOps (2018-Pres) | Modern Modular Cloud Services (Boards, Pipelines)
+--------------------------+

```

---

### Q9. What is "Scrum of Scrums," and how does it help scale large agile teams?

* **Company Asked**: Adobe
* **Source**: Video Session

#### Answer

Scrum of Scrums is a scaled agile technique that connects multiple cross-functional teams working on the same project. It provides a collaborative framework where team representatives (often Scrum Masters) sync daily to discuss inter-team dependencies, blockers, and cross-project integration paths.

```
Team Alpha Scrum  ---> (Scrum Master A) -----\
                                              v
Team Beta Scrum   ---> (Scrum Master B) ---> [ SCRUM OF SCRUMS ]
                                              ^
Team Gamma Scrum  ---> (Scrum Master C) -----/

```

---

### Q10. Why are YAML pipelines favored over Classic UI pipelines in modern infrastructure setups?

* **Company Asked**: Oracle
* **Importance**: **Marked as Important** (Highly asked in architectural evaluation rounds)

#### Answer

YAML pipelines define CI/CD workflows entirely as code (**Pipelines as Code**). This ensures that build tracking steps are version-controlled alongside application source code, allowing changes to be code-reviewed, audited via pull requests, and easily rolled back across parallel branches.

```
[Source: Internet - YAML vs Classic Design]
Classic UI Pipeline:
Stored as hidden system metadata DB configurations -> Untrackable changes via web console modifications.

YAML Pipeline Code Pattern:
[Git Repo Workspace] ---> Contains "azure-pipelines.yml" ---> Audited, trackable, and version-controlled.

```

---

## ## DevOps Architect Tips & Strategy

> **Authoring Perspective:** *DevOps Architect with 20+ years of enterprise systems validation experience.*

1. **Enforce Strict Branch Policies & Ephemeral Workspace Controls**: Never allow direct code modifications inside production paths. Configure structural verification gates requiring successful dry-runs (`terraform plan`) and automated static code analysis checks before any Pull Request can be merged into `main`.
2. **Consolidate Multi-Project Compute Pools**: Avoid micro-managing distinct virtual machine build run environments inside separate projects. Instead, implement shared, centralized self-hosted agent clusters at the Organization layer using elastic Kubernetes scale architectures.
3. **Treat Pipelines completely as Version-Controlled Code**: Migrate away from legacy Classic UI design pipelines immediately. Implement declarative YAML workflows to ensure configuration changes can be fully audited, code-reviewed, and rolled back when necessary.
