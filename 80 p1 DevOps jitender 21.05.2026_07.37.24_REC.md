Here is the detailed interview-ready note as per your instructions.

---

## 1. To-the-Point Summary

This module focuses on setting up a continuous integration (CI) pipeline using **Azure DevOps YAML Pipelines** to automate **Terraform** deployments. Key highlights include:

* Transitioning from Classic (GUI-based) pipelines to **Infrastructure as Code (IaC)** standard YAML templates.
* Understanding the structural components of a pipeline: **Stages $\rightarrow$ Jobs $\rightarrow$ Steps $\rightarrow$ Tasks/Scripts**.
* Configuring multi-command inline bash scripts within a step framework (`script:` syntax).
* Troubleshooting dependency issues on Microsoft-hosted agents (e.g., executing Terraform on a runner lacking the module binary).
* Securing sensitive parameters utilizing **Azure Pipelines Variable Blocks** and integrating **Azure Key Vault** to avoid code credentials exposition.

---

## 2. Detailed Technical Notes

### Structure of Azure DevOps YAML Pipelines

An Azure DevOps YAML pipeline follows a hierarchical structure to execute automated workflows:

1. **Pipeline:** The entire workflow logic composed of one or more stages.
2. **Stages:** Major milestones or logical divisions within a pipeline (e.g., Build, Test, Deploy). Multiple stages run in parallel by default unless explicit dependencies are declared.
3. **Jobs:** Execution units allocated to run on a specific pool or agent. Multiple jobs inside a single stage run in parallel by default.
4. **Steps / Tasks:** Sequential operations executed inside a single job block. These run sequentially.

```
+-------------------------------------------------------+
|                       Pipeline                        |
|   +-----------------------------------------------+   |
|   |                    Stage A                    |   |
|   |   +-----------------------+---------------+   |   |
|   |   |         Job 1         |     Job 2     |   |   |
|   |   |  (Steps run serially) |  (Parallel)   |   |   |
|   |   +-----------------------+---------------+   |   |
|   +-----------------------------------------------+   |
+-------------------------------------------------------+

```

### Implementing Step-Level Execution

When initializing a basic pipeline, utilizing a single implicit job/stage helps test script structures. By defining a minimal execution model, developers can run basic scripts directly on a hosted machine (e.g., `ubuntu-latest`).

```yaml
# Basic step-level execution model
steps:
- script: echo "Hello World"
  displayName: 'Run a one-line script'

```

#### Steps to Configure a New YAML Pipeline:

1. **Navigate to Pipelines:** Select **Pipelines $\rightarrow$ New Pipeline**.
2. **Select Source Code Registry:** Choose **Azure Repos Git** (or GitHub/Bitbucket).
3. **Select Repository:** Choose your targeted repository (e.g., `candy`).
4. **Configure Pipeline Structure:** Choose **Starter Pipeline** to initialize a clean template. Delete the template defaults to specify precise steps.

---

### Executing Multi-Command Scripts & Inline Processing

To run multiple operations within a single agent block without breaking execution contexts, use the **pipeline multiline indicator (`|`)**.

Using the explicit `script:` directive acts as an abstraction layer: it automatically determines the shell footprint of the host runner (Bash on Linux/macOS, Cmd/PowerShell on Windows).

```yaml
# Multi-command pipeline validation sequence
steps:
- script: |
    ls -ltr
    echo "Hello World"
    cat main.tf
  displayName: 'Execute File Check and Configuration Readout'

```

* **Inline Symbol (`|`):** Allows formatting multi-line commands sequentially under a single script block. Every sub-line maintains proper indentation alignment matching the first string position.

---

### Integrating Terraform into Azure YAML Pipelines

To orchestrate infrastructure code dynamically inside a continuous workspace, commands such as `terraform init` and `terraform plan` must be integrated into automated tasks.

#### The Module Resolution Error

When raw Terraform instructions are executed directly on a generic Microsoft-hosted agent without introducing its operational binaries, the runner returns a termination error:

```bash
/agents/_work/_temp/20ea36bf.sh: line 1: terraform: command not found
##[error]CmdLine exited with code '127'.

```

#### Resolution Strategy:

You must explicitly configure repository sourcing hooks to download and setup the targeted application binary package directly on the runtime container prior to processing the execution parameters.

```yaml
steps:
- script: |
    wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com/cloud-hosted-apt focal main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt update && sudo apt install terraform -y
    terraform init
    terraform plan
  displayName: 'Install HashiCorp Binary and Execute Terraform'

```

---

### Environment Authentication Security (Avoiding Credential Leaks)

Hardcoding infrastructure access keys, tenant tokens, or client secrets inside a shared YAML version-controlled directory violates core DevSecOps governance frameworks.

#### Secure Variable Ingestion Architectures

```
[ Azure Repos YAML Pipeline ] 
       │ Ingests tokens securely via $(VAR_NAME)
       ▼
[ Variable Group (Library) ] <─── Link Option ───> [ Azure Key Vault (Secrets) ]

```

#### In-Code Variables Extraction Matrix

Instead of storing plaintext values directly inside the environment block `env:`, bind environmental definitions directly into secure parameter blocks calling localized secrets configurations.

```yaml
variables:
  ARM_SUBSCRIPTION_ID: '84332b29-2a25-4acb-9771-c72ad6d864df'
  ARM_TENANT_ID: '96733b7a-fa01-4fd1-a931-eef951be47c7'

steps:
- script: |
    terraform init
    terraform plan
  env:
    ARM_SUBSCRIPTION_ID: $(ARM_SUBSCRIPTION_ID)
    ARM_TENANT_ID: $(ARM_TENANT_ID)
    ARM_CLIENT_ID: $(ARM_CLIENT_ID)
    ARM_CLIENT_SECRET: $(ARM_CLIENT_SECRET)

```

#### Steps to Configure a Secure Variables Registry Group:

1. Navigate to **Pipelines $\rightarrow$ Library**.
2. Select **+ Variable group** and declare a descriptive group moniker.
3. To protect client secrets, toggle the **Lock icon ($\mathbf{\theta}$)** adjacent to the value entry block. This converts plain strings into irreversible hashed variables, masking logs and restricting pipeline outputs.
4. **Enterprise Option:** Toggle **Link secrets from an Azure key vault as variables**, select the relevant Azure subscription model, verify Key Vault access, and fetch credentials directly without localized parameter management.

---

## 3. Interview Questions Sections (Discussed by Trainer)

* What is the basic hierarchical architecture model inside an Azure DevOps YAML pipeline?
* How do you execute multiple commands within a single step using the inline multi-line formatting block?
* What does error code `127` indicate during an execution run, and how do you resolve binary initialization problems on hosted agents?
* How does the `script:` keyword handle platform differences when executing commands across Windows and Linux-hosted runners?
* Why should you use Azure DevOps Library Variable Groups instead of hardcoding secrets in YAML files?

---

## 4. Top 10 Internet DevOps Interview Questions

> **Important Level Classification:** Marked as **[IMPORTANT]** if historically requested across multiple cross-functional infrastructure interview boards.

### Q1. Explain the difference between Azure DevOps Classic Pipelines and YAML Pipelines.

* **Company Asked:** Microsoft, Accenture, Capgemini
* **Answer:**
* **Classic Pipelines:** Configured via a graphical user interface (GUI). Easier for beginners but lacks version control history and rollback features.
* **YAML Pipelines:** Follows **Pipeline as Code (PaC)** principles. Configuration files are stored alongside application code in version control system (VCS), enabling pull requests, branch policies, code reviews, and structural reuse.


* **Diagram (Source: Internet):**
```

```



Classic:  [ Web UI Click Configuration ] ───► [ Detached Storage ]
YAML:     [ git commit pipeline.yaml ]    ───► [ In-Repo Version Control ]

```

### Q2. How can you share variables across multiple stages or jobs in an Azure DevOps YAML pipeline? [IMPORTANT]
*   **Company Asked:** TCS, Cognizant, Wipro
*   **Answer:** Local variables are scoped directly within their parent block (job or stage). To bridge variables globally across multiple nodes, create an output variable using specific runner log instructions, then pass it using the `dependencies` mapping syntax block:
    ```bash
echo "##vso[task.setvariable variable=myOutputVar;isOutput=true]Value"

```

* **Diagram (Source: Internet):**
```

```



[Job 1 (Sets Task Output)] ─── System Map Pipeline Context ───► [Job 2 (Reads via Dependencies block)]

```

### Q3. Explain the difference between Microsoft-Hosted Agents and Self-Hosted Agents. When would you prefer which? [IMPORTANT]
*   **Company Asked:** Amazon, Deloitte, Tech Mahindra
*   **Answer:**
    *   **Microsoft-Hosted:** Maintenance, scalability, upgrades, and provisioning are fully managed by Microsoft. Each job runs inside a clean, isolated virtual machine instance that is torn down post-execution.
    *   **Self-Hosted:** Managed locally on custom infrastructure. Useful when pipelines require specific software binaries pre-installed (avoiding continuous runtime installation steps), persistent machine caches, or direct line-of-sight network connectivity to private virtual networks.
*   **Diagram (Source: Internet):**
    ```
    Microsoft-Hosted: [ Clean Isolated VM Instance ] ───► [ Execute Job ] ───► [ Destroy Container Instance ]
    Self-Hosted:      [ Local On-Prem Server Base  ] ───► [ Persistent Cache ] ───► [ Retain State Storage ]

```

### Q4. What are Variable Groups in Azure DevOps, and how do you integrate them into a YAML file?

* **Company Asked:** HCL, Infosys, IBM
* **Answer:** Variable Groups are centralized variable blocks created within the **Library** section of Azure DevOps. They allow variables to be shared securely across multiple pipelines within a project. To consume a variable group inside your pipeline, reference its exact name under the variables declaration block:
```yaml
variables:
- group: enterprise-shared-vault-group

```



```
*   **Diagram (Source: Internet):**
    ```
    [ Pipeline Alpha ] ──┐
                         ├──► Ingest [ Shared Variable Group (Library Vault) ]
    [ Pipeline Beta  ] ──┘

```

### Q5. What is an Azure DevOps Service Connection, and why is it required? [IMPORTANT]

* **Company Asked:** EY, PwC, Oracle
* **Answer:** Service connections provide a secure method for Azure DevOps pipelines to interact with external services, cloud platform resources, or third-party APIs (e.g., Azure Subscriptions, Kubernetes Clusters, SonarQube platforms) without exposing raw username and password credentials. They typically leverage secure mechanisms like OAuth tokens, Service Principal Objects, or Workload Identity Federation keys.
* **Diagram (Source: Internet):**
```
[ Azure DevOps Pipeline ] ───► [ Service Connection (Service Principal token) ] ───► [ Target Azure Portal ]

```



```

### Q6. How do you handle pipeline parallelization and control job execution sequences?
*   **Company Asked:** Cisco, Adobe
*   **Answer:** By default, all stages and jobs inside a pipeline stage execute concurrently if multiple hosted agent licenses are available. To enforce a specific execution order, use the `dependsOn` configuration keyword. If a node requires a specific condition to be met before running (e.g., executing a rollback block only on failure), apply the `condition:` modifier block.
*   **Diagram (Source: Internet):**
    ```
[ Stage: Test Code ] ─── dependsOn ───► [ Stage: Provision Infra ] ─── dependsOn ───► [ Stage: Deploy App ]

```

### Q7. What are Deployment Jobs in Azure DevOps YAML, and how do they differ from standard jobs? [IMPORTANT]

* **Company Asked:** Walmart, Target Corporation
* **Answer:** A deployment job is a specialized job framework designed to deploy applications to target environments. It natively provides deployment safety controls, tracks execution history across targeted infrastructure registries, and supports automated execution strategies like **RunOnce**, **Rolling**, and **Canary** out of the box.
* **Diagram (Source: Internet):**
```
Deployment Job: [ Pre-Deploy Step ] ───► [ Deploy Target ] ─── On Failure ───► [ Auto-Rollback Routine ]

```



```

### Q8. How do you integrate Azure Key Vault into Azure Pipelines securely?
*   **Company Asked:** HSBC, Barclays
*   **Answer:** 
    1. Navigate to **Pipelines $\rightarrow$ Library**, select **+ Variable Group**, and toggle the option to **Link secrets from an Azure key vault**.
    2. Map the required keys from Azure Key Vault into the group variable attributes list.
    3. Alternatively, invoke the explicit `AzureKeyVault@2` download task block directly inside your step sequence to pull target secret credentials straight into the pipeline agent's runtime memory at runtime.
*   **Diagram (Source: Internet):**
    ```
    [ Pipeline Execution Agent ] ─── Task: AzureKeyVault@2 ───► [ Secret Vault ] ─── Injects ───► [ Agent Memory ]

```

### Q9. What is a pipeline trigger? Explain the difference between CI, PR, and Scheduled triggers.

* **Company Asked:** LTI Mindtree, NTT Data
* **Answer:**
* **CI Trigger:** Automatically kicks off a pipeline run as soon as code changes are pushed or merged into targeted repository branch lines.
* **PR Trigger:** Triggers validation execution workflows dynamically on open Pull Requests to evaluate changes before merging code into main branch lines.
* **Scheduled Trigger:** Executes automated workflows at specific intervals using a standard **cron expression engine syntax format**.


* **Diagram (Source: Internet):**
```
Developer Action ───► Push Change (CI) / Open PR (PR Verification) / Cron Clock Timer ───► [ Launch Pipeline ]

```



```

### Q10. How can you define step templates to reuse pipeline steps across multiple repositories? [IMPORTANT]
*   **Company Asked:** global technology and engineering firms
*   **Answer:** Common step routines can be stored in a centralized YAML configuration file. This template file can then be referenced by other pipelines across different repositories using the `template` directive and a `resources` block.
    ```yaml
    # In main configuration file
    steps:
    - template: templates/terraform-install-template.yml

```

* **Diagram (Source: Internet):**
```
Central Template Repo [ custom-steps.yml ] <─── Injected via template block ─── [ Main Repo Pipeline YAML ]

```



```

---

## 5. Architectural Recommendations & Suggestions
*From a DevOps Architect with 20+ years of enterprise experience:*

1.  **Eliminate Ad-Hoc Runtime Installs (Optimize Container Strategies)**
    *   *Architect Recommendation:* Avoid continuously executing step tasks like `sudo apt install terraform -y` inside hosted pipeline runs. Running ad-hoc installations adds runtime overhead to every single pipeline execution and creates structural risks if package availability changes upstream. Instead, build custom Docker runner image archetypes that come pre-packaged with fixed, tested versions of all required binaries (e.g., Terraform, Azure CLI, Ansible). This ensures faster, reproducible execution states across all environments.
2.  **Enforce Immutable Environment Secrets Isolation**
    *   *Architect Recommendation:* Never store environmental variable blocks or API subscription keys in plaintext within your code repositories. Even masking non-prod keys can lead to misconfigurations or exposure down the line. Use Azure DevOps Variable Groups linked directly to an isolated instance of **Azure Key Vault**. Apply role-based access control (RBAC) to separate production keys from non-production keys, ensuring access tokens are fetched securely into agent memory only when required.
3.  **Deconstruct Pipelines into Modular Micro-Templates**
    *   *Architect Recommendation:* Treat your infrastructure deployment scripts with the same engineering rigor as application code. Instead of writing monolithic pipelines, modularize workflows into reusable micro-templates grouped by function (e.g., initialization, validation checking, execution blocks). Store these in a central, protected repository. This approach allows enterprise teams to enforce standard practices for logging, security scanning, and cost controls uniformly across all projects.

```
