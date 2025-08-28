# OpenTofu New Providers Working Group

## Problem Statement

The Terraform provider ecosystem is robust, mature, and serves the community incredibly well. OpenTofu remains fully committed to supporting Terraform providers - they're not going anywhere, and we're grateful for the extensive ecosystem and community that has built up around them.

However, we've identified opportunities where OpenTofu-native providers could enhance the ecosystem while maintaining full compatibility with existing Terraform providers.

The issue at hand was being partially addressed in the draft RFC proposed by James here: https://github.com/opentofu/opentofu/pull/3080 but it was quickly identified that we should co-ordinate some product design around this first to understand the full scope.


## Working Mode / Expected Outcome

As this is the first working group set up by OpenTofu, we ask that the process stays quite flexible, however we would like to favor asynchronous communication where possible.

An initial kickoff meeting should happen to open up some discussion around the points below, but also to discuss who can work on which area should happen at the start of the project.

This meeting then can be followed up with some asynchronous work, and possibly communication in a slack channel in the CNCF workspace (Open to all for transparency).

All notes should be shared in the github repository `opentofu/org` in the wg directory so that no information is lost.

There is no current set timeline for this project, however we should try and set one in the initial kickoff meeting.


## Goals

This working group aims to produce research results and specifications that should inform future OpenTofu provider development:

**Protocol Specification:**
- Define a clear protocol specification for OpenTofu-native provider communication
- Document capability negotiation and extensibility mechanisms
- Establish compatibility and versioning strategies

**Feature Catalog:**
- Comprehensive inventory of blocked features from existing HashiCorp Terraform, and OpenTofu issues
- Analysis of which features could be unlocked with OpenTofu-native providers
- Prioritization framework for potential capabilities

**Development Artifacts:**
- Specification for provider client libraries and communication layers
- SDK requirements and design for multiple programming languages (Python, TypeScript, Go, etc.)
- Development tooling and testing framework recommendations

**RFC Completion:**
- Finalize and refine the existing OpenTofu Provider RFC (#3080)
- Incorporate working group findings and community feedback
- Prepare technical specification for TSC review

**Deployment and Execution Models:**
- Remote provider execution strategies and security considerations
- Sandbox environment specifications for safe provider execution
- Federated authentication integration patterns for cloud-native deployments
- Local vs. remote execution trade-offs and recommendations

**Future Vision:**
- Long-term roadmap for OpenTofu provider ecosystem evolution
- Integration strategies with existing tooling and platforms
- Community adoption and migration pathways

## Members

<!-- 
Initial participants and their affiliations
Leadership structure (chairs, co-chairs)
How to join the working group
-->

[TODO - Collate list of members]

## Resources and References

<!-- 
Links to relevant documentation
Related projects and initiatives
Background reading materials
-->

- [New OpenTofu Providers draft RFC - @Yantrio](https://github.com/opentofu/opentofu/pull/3080)


## State of Play

### Unlocking New Functionality

OpenTofu has repeatedly discussed features that would significantly improve the developer experience but are difficult to implement with the current provider architecture. These include:

- **Bulk operations** for efficient batch create/update/delete of resources
- **Resource listing** to discover and import existing infrastructure 
- **Middleware hooks** for cross-cutting concerns like cost estimation, policy enforcement, and audit logging
- **Custom type definitions** to allow stricter type checking across the config
- **Granular state management** with per-object storage and flexible locking mechanisms instead of monolithic state files

Each time the team has identified these opportunities, the response has been "we can't do this without a fundamental change to providers." This list is likely incomplete - one of this working group's first tasks will be to comprehensively catalog all the functionality that OpenTofu-native providers could enable.

OpenTofu-native providers would finally unlock this backlog of innovation while maintaining full compatibility with existing Terraform providers.

An initial effort to identify such issues is ongoing already with the `provider-protocol` label. You can find such issues [here](https://github.com/opentofu/opentofu/issues?q=sort%3Aupdated-desc%20is%3Aissue%20is%3Aopen%20label%3Aprovider-protocol)

### Empowering the End User

The current Terraform provider ecosystem is fantastic at serving broad use cases through company-authored providers (AWS, Azure, Google Cloud, etc.). These providers are built by the companies or other large entities, whose services they integrate with, go through formal registry processes, and are designed for mass consumption across the community.

However, there's a different persona we want to serve: **end users** - developers, DevOps engineers, platform teams, small companies, and infrastructure practitioners who need custom tooling for their specific environments. These users often need:

- **Internal company APIs** wrapped as OpenTofu resources
- **Custom deployment workflows** that don't fit standard provider patterns  
- **One-off integrations** between existing systems and infrastructure
- **Rapid prototyping** of infrastructure concepts without formal provider overhead
- **Personal automation tools** that deploy alongside their configurations

Unlike broad-use Terraform providers, these custom providers don't need to go through registries or serve thousands of users. They need to be **easy to create, test locally, and deploy alongside configuration** without the ceremony of formal provider development.

Additionally, many capabilities currently exist only in Terraform Automation and Collaboration Software (TACOS) platforms like Atlantis, Spacelift, Env0, Terraform Cloud etc - security scanning, policy execution, cost management, and compliance checking. These tools have historically relied on planfile analysis as their primary integration point. OpenTofu-native providers should aim to open up this functionality for users who don't use TACOS platforms while also providing better integration points for TACOS platforms themselves.

### Simplifying the Development Process

Recent innovations in plugin architectures demonstrate how dramatically development can be simplified. The Model Context Protocol (MCP) has shown that complex integrations can be made accessible through:

- **Multi-language SDKs** with idiomatic APIs in Python, TypeScript, and other languages
- **Simple protocol specifications** that abstract away complex communication details
- **Lightweight development workflows** that enable rapid prototyping and iteration
- **Open standards** that encourage community contribution and experimentation

MCP servers can be built in dozens of lines of code rather than hundreds, enabling developers to focus on business logic rather than protocol implementation. This has led to an explosion of community-built integrations and creative use cases.

OpenTofu-native providers could follow this same pattern - providing simple, well-documented SDKs across multiple languages that handle the complex protocol details behind the scenes. This would enable:

- **Rapid prototyping** of infrastructure concepts
- **Domain experts** building providers in their preferred languages
- **Lower barriers to contribution** encouraging more community participation
- **Faster innovation cycles** with simplified testing and development workflows

By learning from successful plugin ecosystems like MCP, OpenTofu can democratize provider development and unlock new levels of community creativity and contribution.

### Provider Model Overlap

OpenTofu-native providers should be designed as a **superset** of the existing Terraform provider model, not a replacement. The new provider architecture should support:

**Core Terraform Functionality:**
- Resources and data sources
- Provider functions
- Ephemeral resources
- Standard lifecycle operations (create, read, update, delete)

**Plus OpenTofu-Specific Extensions:**
- Bulk operations and resource listing
- Middleware hooks and cross-cutting concerns
- Custom type definitions and enhanced validation
- Granular state management capabilities
- Advanced integration points for tooling

This design should ensure that OpenTofu-native providers can serve both personas:

1. **End users** who need simple custom tooling can write lightweight providers focusing only on basic resource/data source patterns
2. **Advanced implementers** can leverage the full OpenTofu feature set alongside traditional provider functionality

While we expect most OpenTofu-native providers will focus on the new functionality rather than reimplementing existing resources and data sources, the architecture should support both approaches. This prevents ecosystem fragmentation and reduces confusion - developers can use familiar patterns while gradually adopting OpenTofu-specific features as needed.

The goal is **additive capability** - traditional provider patterns should work exactly as expected, but with optional access to powerful new features when the use case demands it.

## What Exists Already

The OpenTofu ecosystem currently supports various integration patterns, each working with different artifacts and outputs:

**Planfile Analysis Tools:**
- **Infracost** estimates costs by parsing plan files
- **Checkov, tfsec, terrascan** perform security scanning on plans
- **Policy engines** like OPA analyze planned changes for compliance

**Output Parsing Integrations:**
- Tools that consume machine-readable JSON output for reporting and analysis
- Custom bash scripts and automation that parse human-readable logs
- CI/CD integrations that react to plan/apply output for notifications and workflows

**Orchestration and Workflow Tools:**
- **Terragrunt** for configuration management and orchestration
- **TACOS platforms** (Atlantis, Spacelift, Terraform Cloud, etc.) that wrap OpenTofu execution
- **Custom workflow engines** that coordinate multiple OpenTofu runs and environments

**Backend and State Integrations:**
- Tools that monitor state changes through HTTP backend APIs
- Custom audit systems that track infrastructure changes via backend monitoring

**Configuration Analysis:**
- Static analysis tools that parse `.tf` files directly
- Configuration generators and templating systems

**Terraform Provider Ecosystem:**
- Mature provider ecosystem with thousands of resources across hundreds of providers
- Well-established patterns for resource lifecycle management

**The Integration Gap:**
All these tools work with OpenTofu's **existing artifacts** rather than being directly integrated into the execution process. But what if we flipped this approach? Instead of tools adapting to work around OpenTofu's outputs, OpenTofu-native providers could provide better integration points for these tools - or even bring some of this functionality directly into OpenTofu itself.

## What We Will NOT Be Addressing

To maintain focus and manage scope, this working group will explicitly **not** address:

**Terraform Provider Compatibility:**
- We will not break or deprecate existing Terraform provider support
- We will not require migration from Terraform providers to OpenTofu-native providers
- We will not modify or extend the existing Terraform provider protocol

**Registry and Distribution:**
- We will not design a new provider registry system
- We will not address provider versioning, signing, or security models
- We will not define provider publishing workflows or governance

**Ecosystem Disruption:**
- We will not recommend replacing existing tools (Infracost, TACOS platforms, etc.)
- We will not fragment the community by creating competing standards
- We will not prioritize OpenTofu-native providers over community compatibility

This working group's purpose is **exploration and research** - to understand what's possible, identify opportunities, and provide recommendations. Any actual development decisions remain with the OpenTofu TSC and core maintainers.

