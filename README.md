# Certified GitOps Associate (CGOA)
<img src="https://training.linuxfoundation.org/wp-content/uploads/2023/03/CertifiedGitOpsAssociate_Badge-300x300.png" width="100">

Curated content for the Certified GitOps Associate (CGOA) certification exam.

According to [The Linux Foundation](https://training.linuxfoundation.org/certification/certified-gitops-associate-cgoa/):
> The Certified GitOps Associate (CGOA) exam allows candidates to demonstrate their understanding of GitOps principles, terminology, and best practices in setting up a GitOps-managed software system in addition to related practices such as Configuration of Code, Infrastructure as Code, DevOps & DevSecOps, and CI & CD and how to map them to GitOps.

## Domains & Competencies
The Certified GitOps Associate (CGOA) exam curriculum can be found [here](https://training.linuxfoundation.org/certification/certified-gitops-associate-cgoa/#:~:text=Domains%20%26%20Competencies).

## GitOps Terminology 20% 
GitOps is a set of principles, best practices, and tools where the entire code delivery process is controlled via Git. It is a developer-centric operational framework for managing both infrastructure and deployments of cloud-native applications. GitOps brings in the familiar workflows built around Git to the operations fold, such as rollouts with merged pull requests and rollbacks with git reverts.

GitOps benefits include:
-	Faster deployment: Increased velocity via reduced operational overhead.
-	Quick and easy rollbacks.
-	Standard workflow: Developer-centric familiar tools and Git workflows.
-	Auditability: capturing and tracing any change to infrastructure through Git history.
-	Single source of truth.
-	Code revision.
-	Drift detection and remediation: The difference between the desired and actual state is monitored and known.
-	Multi-environment consistency: reliably and consistently configure multiple environments and deployments.

The implementation of the GitOps operator seems fairly straightforward on the surface. In principle, it is simply cloning the Git repository containing manifests and letting `kubectl diff` and `kubectl apply` do their magic to keep things in sync. Whenever the `kubectl apply` makes a change, it stores the input manifest within the `kubectl.kubernetes.io/last-applied-configuration` annotation. When the command is executed again, it checks the annotation to see where things left off before making any new changes. This enables kubectl to execute a three-way diff/merge and properly handle cases where fields are removed from resource manifests.

In summary, a GitOps operator is a continuously running control loop that performs the following:
-	Cloning the repository.
-	Examining the cloned repository for Kubernetes manifests to apply to the cluster.
-	Applying all detected manifests onto the cluster using `kubectl apply`.

The simplest way to implement this control loop is as a Kubernetes CronJob that runs on a schedule.

### Continuous
In GitOps terminology, continuous typically refers to the continuous and automated synchronization of the desired state of a system with the state declared in a Git repository. The reconciliation continues to happen, not that it must be instantaneous.
### Declarative Description
A declarative description refers to the practice of defining the desired state of a system in a declarative manner. The declarative nature of the description means that it defines what the system should look like rather than detailing the sequence of actions to get there. This separates configuration (the desired state) from the implementation (commands, API calls, scripts, etc.) used to achieve that state.
### Desired State
The desired state refers to the target or intended configuration of the system, infrastructure, or application that is sufficient to recreate the system so that instances of the system are behaviorally indistinguishable. The desired state is defined in version-controlled Git repositories. This configuration data generally does not include persistent application data, e.g., database contents, though often does include credentials for accessing that data, or configuration for data recovery tools running on that system.
### State Drift
State drift refers to the condition where the actual state of a system, infrastructure, or application deviates from its desired state. This deviation can occur due to various reasons, such as manual changes made directly to the system.
### State Reconciliation
State reconciliation in GitOps aligns the current state of a system with its desired state through a continuous process involving comparison, drift detection, automated correction, and a continuous loop. Contrary to traditional CI/CD where automation is generally driven by pre-set triggers, in GitOps, reconciliation is triggered whenever there is a divergence.
### GitOps Managed Software System
A software system or an application that is orchestrated using the GitOps methodology. In this context, GitOps serves as the operational model for managing the software system lifecycle. A software system managed by GitOps includes:
- One or more runtime environments consisting of resources under management.
- The management agents within each runtime.
- Policies for controlling access and management of repositories, deployments, and runtimes.
### State Store
A system for storing immutable versions of desired state declarations. A state store serves as a source of truth for the desired state of a system or infrastructure. This state store should provide access control and auditing on the changes to the Desired State.
### Feedback Loop
The feedback loop is a continuous process that involves monitoring, evaluation, and iterative adjustments based on insights gained from observing the system's behavior.
Open GitOps follows control theory and operates in a closed loop. In control theory, feedback represents how previous attempts to apply a desired state have affected the actual state. For example, if the desired state requires more resources than exist in a system, the software agent may make attempts to add resources, to automatically roll back to a previous version, or to send alerts to human operators.
### Rollback
Rollback refers to the process of reverting a system, infrastructure, or application to a previous state stored in version-controlled Git repositories.

## GitOps Principles 30%
The GitOps principles are derived from modern software operations but are also rooted in pre-existing and widely adopted best practices.

The desired state of a GitOps managed system must be:

### Declarative
A system managed by GitOps must have its desired state expressed declaratively. The entire system, infrastructure and applications, is described declaratively.
### Versioned and Immutable
The desired state is stored in a way that enforces immutability and versioning and that retains a complete version history. Git is the canonical example of the state store. The canonical, immutable desired system state is versioned in Git, the single source of truth retaining the complete version history.
### Pulled Automatically
Software agents (GitOps controllers) automatically pull the desired state declarations from the source at regular intervals. Approved changes are automatically applied to the system.
Triggering updates via webhooks is not GitOps.
### Continuously Reconciled
Software agents continuously observe the actual system state and attempt to apply the desired state. Reconciliation does not have to be instantaneous.

## Related Practices 16%
To comprehensively understand GitOps, it is essential to explore its integration with various related practices that collectively contribute to the optimization of software delivery pipelines. Each practice brings unique methodologies and toolsets to the table, working synergistically to enhance efficiency, scalability, and reliability in software development and deployment processes.

### Configuration as Code (CaC)
Configuration as code (CaC) is a practice that treats infrastructure and application configuration data as code.
### Infrastructure as Code (IaC)
Infrastructure as code is a practice for managing, configuring, and updating the entire software infrastructure using declarative configuration files instead of manual processes. OpenTofu, a Terraform fork, is a composable infrastructure automation tool that supports all major cloud providers. Major cloud providers have developed their own solutions, which are specifically designed to take advantage of their cloud infrastructure, such as AWS CloudFormation and Azure Resource Manager (ARM) templates.

Best practices for IaC adoption include:
-	Version Control: Share IaC code in a VCS. Codifying institutional knowledge reduces future risk.
-	Automated Testing and Vulnerability Scanning: Run automated tests and static code analysis.
-	Security: Pipelines should be the only way to apply changes to the infrastructure. Use variables set as sensitive values when passing secrets into Terraform to avoid displaying them in output or logs.
-	Idempotency: Apply IaC frequently, even if it has not changed.
### DevOps and DevSecOps
DevOps culture is a philosophy that aims to break down silos between software development and IT operations to deliver software faster and more reliably. DevOps helps organizations maximize productivity, shorten delivery times, and encourage more collaborative and iterative work.

Site Reliability Engineering (SRE): As defined in the Google SRE book, toil is manual, repetitive, automatable, and tactical work. The best SRE and platform teams identify toil and work to eliminate it. SRE and DevOps are complementary philosophies.

DevSecOps is a collaborative approach that integrates security practices at every phase of the software development lifecycle to deliver robust and secure applications.
### CI and CD
CI/CD stands for Continuous Integration (CI) and Continuous Delivery or Deployment (CD). It is one of the prominent DevOps practices that automates the building, testing, and releasing of software applications. It consists of several stages, such as code analysis, unit testing, integration testing, etc. CI/CD aims to deliver high-quality software faster and more frequently while reducing manual errors and bottlenecks.

Continuous Integration is a software development practice where developers frequently integrate their work into a shared repository. Each integration is automatically verified by building the project and running tests, allowing teams to detect and address issues early in the development cycle.

Continuous Delivery or Deployment is the ability to get code changes of all types into production quickly and reliably in a sustainable way. It automates the manual, error-prone process of releasing code into production. 
The main difference between Continuous Delivery and Continuous Deployment is that the production release is gated with a requirement for a human to approve it manually, which is not the case with the latter.

## GitOps Patterns 20%
This section touches on common GitOps patterns.

### Deployment and Release Patterns
In the case of Kubernetes, the process of a traditional deployment is as below:
-	A developer commits a change to the application’s source code.
-	A CI system builds the application and may also perform additional actions such as unit tests, security scans, etc. The CI system creates a container image that is pushed to a registry.
-	The CI platform, with direct access to the Kubernetes cluster, creates a deployment using a variation of the `kubectl apply` or `helm install` command. 

With GitOps, the process is as below:
-	A developer commits a change to the application’s source code.
-	A CI system builds the application and may also perform additional actions such as unit tests, security scans, etc. The CI system creates a container image that is pushed to a registry.
-	A human or an automated process changes the Kubernetes manifests in the Git repository.
-	A dedicated GitOps controller that is running inside the cluster monitors the Git repository. As soon as a change is made, it pulls the changes and applies them to the cluster.
### Progressive Delivery Patterns
Progressive Delivery is the practice of deploying an application in a gradual manner allowing for minimum downtime and easy rollbacks. There are several forms of progressive delivery such as blue/green, canary, A/B, and feature flags.

Blue/Green deployment is a deployment strategy where two identical production environments are maintained. At any given time, only one of these environments serves live traffic while the other remains idle. This setup is one of the simplest ways to minimize deployment downtime.

Canary deployment is similar to blue/green, but instead of switching 100% of live traffic all at once to the new version, it can be switched only to a subset of users.
### Pull vs. Event-driven
Pull-based GitOps: The GitOps operator continuously polls the Git repository to detect changes in the desired state. When changes are identified, the operator initiates the synchronization process to bring the actual state in line with the updated configuration.

Event-Driven GitOps: The GitOps operator is triggered by events, such as Git webhooks, rather than continuously polling the repository. Changes in the Git repository trigger the events that initiate the synchronization process.

Push Model: The deployment (mostly performed by CD pipelines) is external to the system and generally managed by the same CI server. CI server can execute `kubectl apply` or `helm install` command to deploy an application to the cluster following a successful run of a CI pipeline.
### Architecture Patterns (in-cluster and external reconciler, state store management, etc.)
In-Cluster GitOps Architecture: The GitOps controller resides within the cluster it manages.

External Reconciler GitOps Architecture: The GitOps controller is external to the cluster it manages.

#### State Store Management
-	The recommended workflow is known as trunk-based development. This method defines one branch as the “trunk” or the “main” branch and conducts development in different short-lived branches for each environment. Once development is completed for a particular environment, the developer creates a PR from the branch to the trunk. Utilize GitHub’s branch protection rules or their equivalents on other Git platforms.
- General best practices when it comes to structuring Git repositories for GitOps include (The main point is to avoid copying the same YAML everywhere): 
  -	DRY (Don’t Repeat Yourself, or YAML). Use Kustomize to keep the base configuration of deployment and store the deltas as patched overlays. 
  -	Parameterization: The Helm lookup functionality shines in cases where, for instance, the host field in the Ingress configuration needs to be populated with the FQDN of the application.

- How many repositories to have and what does the Git repository structure look like? 
  -	It depends on how the organization is structured and where the boundaries lie. 
  -	There could be certain limitations associated with the GitOps tool being used.
 
- Two common patterns arise when it comes to the structure of repositories: Monorepo and Polyrepo. 
  -	Monorepo: Monorepo: All manifests, including applications, cluster configuration, and cluster bootstrapping live in the same Git repository.
Cons: Potential scalability and performance issues (case of Argo CD). 
  -	Polyrepo: A polyrepo setup consists of multiple repositories to support a number of clusters or deployment environments. Use cases include separating concerns and multitenancy. 
Cons: An increased number of Git repositories to manage.

-	Should Kubernetes manifests be stored in the same repository as the application code or in a separate, dedicated repository?

 	It depends. For small teams, keeping everything together in one place makes sense. However, for larger teams with many clusters, splitting cluster-specific YAML manifests from application files into a separate repository might be a better choice.
-	By applying Conway’s law to GitOps, each development team is expected to create their own branches and directories within a repository. Separate development in directories, not Branches.

- Directory Structure is going to be driven by the organizational structure and possibly by regulatory requirements as well.
  -	Repositories Reflecting an Organizational Boundary: A case where there is an organizational boundary between the Kubernetes platform administrator and the Kubernetes application developer.
    -	The Kubernetes administrator's repository is mainly geared toward getting the Kubernetes cluster bootstrapped and configured with the essential components to run applications.
    -	The Kubernetes application developer's repository setup is pretty straightforward. The bulk of the configuration resides in the base directory, with only the deltas stored in overlays.
  -	The GitOps Repository demonstrates a layout that adheres to the DRY principle and maintains a generic structure suitable for deployment across multiple clusters. It assumes “full DevOps” wherein the entire organization, including Kubernetes administrators and application developers, collaborates in the release process.
 
#### Secrets Management
-	Storing sensitive data in a Git repository, even if it is private and secured behind a firewall, presents a security risk.
- Two common approaches have emerged to securely incorporate Kubernetes secrets into GitOps workflows:
  -	Storing encrypted secrets in Git.
  -	Storing secrets in external services and keeping references to the secrets in Git.
-	[Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) is a Kubernetes controller that converts sealed secrets to plain secrets that can be used in applications. Sealed secrets are an extension built on top of Kubernetes native secrets. This means that once the encryption/decryption process is complete, all secrets function just like standard Kubernetes secrets. The controller comes with the associated kubeseal executable that is created for encrypting secrets. Encrypted secrets can only be used in the cluster that was used for the encryption process, i.e., all secrets are cluster and namespace specific.
-	[External Secrets](https://github.com/external-secrets/external-secrets) is a Kubernetes operator that integrates external secret management systems such as HashiCorp Vault and Azure Key Vault using a plug-in model. The External Secrets operator reads information from external APIs (for example, HashiCorp Vault) and injects the values from the external system into Kubernetes as a secret. This process works by providing the controller with an ExternalSecret object. This object has a secretStoreRef field where a user defines a SecretStore, which has information about how to fetch the required secret. 
-	Using an external secret management system technically does not conform to GitOps principles.

## Tooling 14%
The tooling section is dedicated to exploring the diverse array of tools and technologies that underpin GitOps practices.

### Manifest Format and Packaging
-	Manifest Format: Refers to the structure and syntax of configuration files (such as YAML) that define the desired state of applications and infrastructure.
-	Packaging: Involves organizing and bundling manifests along with associated resources and dependencies. Tools like Helm charts or Kustomize facilitate the process by encapsulating configurations in a reusable and shareable manner.
-	[Kustomize](https://kustomize.io) is a framework for patching–or selectively altering–files, built into Kubernetes. It is a configuration manager that allows customizing untemplated YAML files without touching the original YAML configuration file. Kustomize is organized in a hierarchical directory structure of bases and overlays. Kustomize allows having a core set of manifests (base) and storing the deltas in directories (overlays).
- [Helm](https://helm.sh) is the package manager for Kubernetes. Helm Charts are used to define, install, and upgrade applications. A Chart is a Helm package that contains all the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. A Release is an instance of a chart running in a Kubernetes cluster.
-	Kustomize was built as a patching framework. Helm was built as a templating engine.
### State Store Systems (Git and alternatives)
Git, from which GitOps derives its name, is the canonical example used as this state store but any other system that meets these criteria may be used. Other options for state stores include OCI registries, Helm repositories, and buckets.
### Reconciliation Engines (Argo CD, Flux, and alternatives)
[Argo CD](https://argoproj.github.io/cd) is a declarative, GitOps continuous delivery controller for Kubernetes. Argo CD acts as the software agent that automatically pulls the desired state from a specific source, and continuously reconciles it with the live state of the cluster.

[Flux CD](https://fluxcd.io) is an open source GitOps tool designed to simplify and automate the deployment and lifecycle management of applications and infrastructure on Kubernetes.

The main difference is that Flux uses the Helm Golang package. Flux does not render the YAML, but instead deals with Helm directly.
### Interoperability with Notifications, Observability, and Continuous Integration Tools
#### Observability
There is a need for two types of observability:
- Internal observability: To effectively detect and rectify configuration drift, the GitOps controller requires visibility into the internal state of the Kubernetes cluster.
- External observability: Other systems operating within and outside the cluster need to be aware of workflows automated by the GitOps engine. For this purpose, the GitOps controller should expose metrics that cloud native monitoring tools can consume.

  External observability has two elements:
  -	A monitoring system must be running in the Kubernetes cluster like Prometheus.
  -	Published metrics generated by the GitOps controller or related systems.
#### Continuous Integration Tools
The CI platform that builds the application relies on the GitOps controller for deployment management. This is primarily done through the CI process starting a PR against the tracked branch for each stage of the pipeline.

The objective of a GitOps CI pipeline is to
-	Build the application and run unit tests.
-	Push a new container image to a container registry.
-	Update the Kubernetes manifests in Git to reflect the new image.

## References
1. The [OpenGitOps](https://github.com/open-gitops) [principles](https://github.com/open-gitops/documents/blob/main/PRINCIPLES.md) and [glossary](https://github.com/open-gitops/documents/blob/main/GLOSSARY.md).
2. [The Path to GitOps](https://developers.redhat.com/e-books/path-gitops) authored by Christian Hernandez.
3. [GitOps and Kubernetes](https://www.manning.com/books/gitops-and-kubernetes) authored by Billy Yuen, Alexander Matyushentsev, Todd Ekenstam, and Jesse Suen.

