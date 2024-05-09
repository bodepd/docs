# Purpose  

This document captures a vision on how a company organizes itself as a set of platforms that drive a seamless developer experience through paved path. This document is intended to capture and align Evolve Cloud on a single vision for: 
 

What a platform strategy built on cloud looks like architecturally 

How shared service platforms are represented to the developer as a Paved Path 

The document explains the design/desired outcomes of a technical system without too many assumptions around underlying technologies so that we can evaluate multiple technology stacks to see how they compare to the design. It can also be used to both sell the vision of what we want to build to customers and align delivery teams around what they need to build. 

# Overview 

 

Cloud providers like AWS, GCP, and Azure provide powerful APIs and event sources that serve as the building blocks for scalable and autonomous software. Although the move to cloud has empowered developers to leverage these APIs to deliver more quickly, an overemphasis on developer productivity has left much of the organization behind because of two seemingly conflicting requirements. 

Allow developers the freedom to move fast and innovate 

Apply strong governance controls 

Evolve Cloud believes these two requirements are not only aligned, but that strong governance controls and standardization facilitate faster development by commoditizing as much of the application stack as possible and allowing developers to focus on exactly they want to innovate. This paper explains how to build an end-to-end solution for managing software in the cloud that leverages strong governance controls to facilitate increased developer velocity. 

Platform Service Catalogue 

 

An efficient enterprise is organized as a set of platforms that provide specific capabilities. Each platform is treated as a single product that can be leveraged across the company and is implemented/operated by dedicated teams with their own product managers, feature backlogs, and roadmaps. Structuring a company as a set of platforms organizes your subject matter experts according to the set of problems they solve and empowers them to build interfaces to solve the problem at scale across your enterprise. 

These platforms are organized into a service catalogue that makes it easy to search for what capabilities an enterprise supports and what technical products provide those capabilities. The service catalogue makes it easy to find all capabilities, the teams that own them, and some designation of maturity (i.e.: is it the recommended solution, on a deprecation path, experimental). The service catalogue is also a central location to find the documentation for how to use a platform, data for how much a platform costs to run, and observability data to understand the overall health of a platform, what Service Level Objectives (SLOs) it defines and how it measures against them. 

This centralized data can help a business understand what capabilities exist, where platforms duplicate a capability, how platforms perform against SLOs, and how they drive cost. This provides a great set of data that a company can use to make informed decisions about where to invest and how to re-organize for efficiency. 

Since this article focuses on developer efficiency, we will be considering Platforms that are consumed by development teams and refer to them as shared services. Examples of a shared service might be Kafka or Nifi platforms for streaming data, a k8s platform for managing applications, or Splunk (observability platform), authentication and authorization. Each of these platforms is included in the service catalogue which is integrated into a developer portal that makes it easier for development teams to integrate their applications with platforms. 

Paved Path 

 

Paved Path simplifies the developer experience by being opinionated about both what tools a developer uses and what processes they follow. As the name implies, “paved path” provides a clear and straightforward route that developers follow from project inception to production. It does this by eliminating choice, and driving a simplified, standard set of interfaces. With Paved Path, developers trade flexibility for simplicity and the organization gets consistency on which strong security can be built on. 

Paved Path simplifies the developer experience for leveraging the “recommended” platforms from the service catalogue and may support multiple use cases like publishers/subscribers, provisioning and routing to web services, or building and deploying ML models. Paved Path is implemented as code generation tools, declarative interfaces for configuring dependencies, as well as a set of self-service APIs and Portals documented in a service catalogue. 

As a paved path matures, it becomes more and more opinionated about what languages, frameworks, data-stores, observability tools, CI and CD processes, or change management processes developers should follow.  

Declarative Specifications 

Platform teams expose interfaces for shared services that can be leveraged by multiple development teams. As a principle, these services should be represented to end users as declarative specifications. 

Declarative specifications are used to abstract away internal implantation details of our platforms so that users can describe a desired end-state understand when that state has been achieved. Consider the following specification for an application with two services. Here a developer specifies a hostname, a list of services, as well as individual paths that route to each service. They aren’t concerned about how the routing is configured (could be nginx or an Isto gateway), the platform team has made those implementation decisions for them and just gives them a way to describe the desired end state. 

application: 

name: danapp 

services: 

- api 

- login 

routes: 

  host: danapp.internal.mydomain.com 

  path: 

    login: "/" 

    api: "/api" 

 

These declarative specifications can be provided in code repositories as configuration or may live behind APIs (or both). Putting them in code repositories provides an easy way to track how they change over time, makes developers more aware of changes, and allows changes to be tested as a part of the SDLC (assuming code validation already exists to changes pushed to designated branches like main or feature branches). 

This can be especially useful as your organization evolves its thinking about these configurations as developer managed to machine managed. Since all revision control offerings provide APIs for opening PRs and changing branches, storing these configurations in source code can allow for ways for automation to request updates in a way that provides visibility to the development team about the changes. However, this still requires that your developers respond to and accept changes. Abstracting details away from developers and putting them behind APIs managed by a platform team allows you to make changes to the platform without involving developers, but it also means that the changes need to be made carefully since the updates are not integrated with the SDLC. 

Foundational Platforms 

 

All cloud-based services and platforms are built upon a set of foundational platforms that provide core services such as authentication, authorization, and cloud account management. 

Authz Foundations 

Cloud Foundations 

 

It should be trivial for developers to provision cloud accounts that confirm to a company’s governance standards through a self-service portal or API and to leverage shared-services/platforms from those accounts. As a principle, each business unit should have their own cloud accounts since they set up a boundary both for costs as well as an authorization scope. Time is better spent making it trivial to provision new accounts per business unit or to make workloads portable to support re-organization that it is trying to figure out how to partition costs or support complex authorization scopes.  

Cloud Foundations automatically create and configure cloud accounts so that they all conform to the same governance processes, and all leverage the same shared services. A central organization (usually called CCoE, cloud center of excellence) is responsible for creating and maintaining the automation code and tooling that drives the cloud foundation and supporting an inner-source model where platform teams can provide standard configurations applied through the Cloud Foundations. The MVP for cloud foundations should be able to do the following: 

Create/View/Update/Delete cloud accounts. 

Specify what meta-data is required for cloud accounts (at a minimum, it should include who owns the account, business unit it supports, and environment). 

Integrate cloud access with an organization's authentication and authorizations tools to provide SSO (single-sign-on) and MFA (multi-factor-authentication) for cloud access. This auth should include access to the cloud provider’s console and APIs, some APIs like K8s may require their own auth strategy. 

Create authorization groups/entitlements that allow account access to the cloud account and authorize the account creator to add users to those authorization groups. 

Auto-tag all resources in an account based on meta-data provided as a part of the provisioning process. 

Egress cloud cost data to a central data-store. 

Provide basic network capabilities: 

A way to route to/from other networks, or to/from the public internet 

IPAM integration to ensure non-overlapping IP addresses (when needed) 

DNS integration so that services can route to each other and between accounts 

For our design, we will assume that a portal exists that is powered by an API and that the API is working together with an orchestration engine. Since, as a principal, we push as much logic to the cloud account as possible, the orchestration engine in our central account provisions orchestration engines into each local account. 
 

 

Any developer can use this portal to create, update, or delete accounts. An account is created together with a set of entitlements used for authorization. When a developer creates an account, they are required to provide additional metadata that will be associated with the account and can be used to drive how it is configured. The metadata for accounts is likely to be different across different companies based on how they want to understand their cloud accounts. Some examples of that meta-data are: 

Account Owner, Secondary Owner 

Business Unit that accounts supports 

Environment (prod, dev, etc.) 

What GitHub organization or repository the account is associated with 

The orchestration engine configures all accounts according to configuration code managed in a git repository. This will include a base set of rules that are applied to all accounts (like tagging and egressing cost data), rules that may be set per environment (like whether to provision a NAT gateway), and specific rules required to enable a shared service. 

To support a Paved Path, cloud accounts need to integrate with shared services from various platform teams. The orchestration system needs to support the automation to enable support for a shared service in accounts. This configuration could be networking related to support routing to a service, provision cross account roles that allow the service access to the cloud apis, or configure components in the cloud account via the orchestration engine. 

The cloud foundation must support inner source, an enterprise strategy where tools support contribution from platforms teams across the company so that each platform team can propose the configuration changes required for their platform to be used from each account and the CCoE can approve those configuration rules. 

Security 

 

The Cloud Foundation should be able to support your security platform team to apply the configurations that they need in cloud account for governance. The cloud foundation and other shared services support security by allowing them to implement preventative and detective controls. For preventative controls, a security team may own the configuration for how entitlements are created for each team and what permissions they are allowed. Depending on your security tools, detective controls may require cross account roles to allow your security tools to read access to APIs or might need to perform configuration to support streaming CloudTrail data. 

Detective controls are useful for understanding if there is a breach, and the security platform needs a way to alert the individuals who are able to resolve the security issues. However, a better pattern is to have corrective controls, I.e., a way to detect when there is an issue and then remediate the issue automatically. If we imagine developer services as being represented as a combination of code and declarative specification in a repository, then corrective controls can be applied in a few ways. If the component that needs to be updated lives a part of the developer’s source code (library definitions, source code, or declarative specification), a central platform should automatically open pull requests to perform the remediation. If the component to be updated lives as a part of the platform, then the platform is responsible for updating it in a way that does not disrupt customer workloads. 

The higher in the stack that our platform abstractions are, the more issues we can resolve at the platform level. For example, if you implement your AaaS layer for bring your own container, then you could build out an abstraction layer that builds the containers from a dockerfile that they provide. This means that the user will be responsible for updating that container if any vulnerability exists. If we instead use buildpacks and convert a user's code into a docker container, then our platform can resolve issues where a base image needs to be updated. 

 

application: 

name: danapp 

services: 

  - api: 

      dockerfile: api/Dockerfile  

  - login: 

      dockerfile: login/Dockerfile 

 
 
 

More Platform 

 

WaaS (Workload as a Service) – Developed applications consist of code written by your developers as well as some set of logic about where and how the code will be deployed. There are typically a few types of workloads (services, event publishers/subscribers, batch jobs), each of these can be expressed by a declarative specification that can describe how a resource is deployed. A service, as shown above, may specify how requests can route to it, how it can be configured, its health checks can be queried, as well as what resources it needs to run and how those resources need to support autoscaling. 

DBaaS (Database as a Service) – Each development team shouldn’t be thinking about how to provision or run databases, instead they should be able to launch from a set of pre-configured databases provided by a central platform team. This team can determine what types of database-backends they want to support and what configurations they should expose, some may be simple for POC, others may implement cross-region replication. The platform team should be thinking about how to simplify what they offer as much as possible as opposed to exposing every possible knob on every database offering. The database team should also be thinking about what client code needs to exist to connect to databases and how best to abstract away the details of how the connections occur either via something like open service broker or through code scaffolding. 

TaaS (Testing as a Service) – An organization should be thinking about what types of tests their development teams are running and how to build platforms to simplify those tests. Some examples to consider are performance testing as a service or chaos engineering as a service. Both can be implemented through a declarative specification (for performance testing, one that shows how many of which requests to hit which endpoints with) 

NaaS (Networking as a Service) - Application teams need to leverage different types of network services for routing traffic (NAT gateways, transit gateways, ingress from internet (Akamai as a service)) and packet inspection. These will likely be built out as a part of your Cloud Foundations platform, discussed in the next section. 

 

Principles 

 

  - Define the overall developer experience 

  - Define the requirements and the capabilities required to satisfy those requirements 

 

This paper covers a set of  

 

NOT READY 

Organize assets as platforms -  

 

Support self-service 

This paper is aligned against a set of principles for how to build platforms. They are defined here separately and will be referenced in the rest of the paper. 

 

Organize Capabilities as Platforms: 

Declarative specifications live in Git 

Organize services as platforms 

Self-service on-boarding -  

Paved path for software developers 

Git is the source of truth 

 

Since cloud accounts serve as boundary for both billing and authorization scopes, business units should be provisioned into a single cloud account. Instead of spending time figuring out how to split up costs within a single account, an organization should instead focus on making it as easy as possible to provision accounts and to move workloads in between accounts. 

Push as much logic as possible to the managed cloud account. As you build both your cloud foundations as well as integrate shared services that can be leveraged by the account owners, you should think about putting as much configuration logic into the account as possible, since consolidating management tasks to a controller account can cause scaling issues. 

 

Support inner sourcing 

Put platforms behind APIs 

Push account management to the account itself (ie: master accounts to perform management won’t scale). Having a single account manage individual resources across a large number of accounts will have scaling issues. 

Reflect configuration changes into the underlying cloud account as close to real time as possible 

Detect configuration drift 

 

Autonomous Platforms 

 

Basically, I want a section that talks about how we can start to move towards automous, machine decision based solutioning once we get this far 

 

References 

 

https://www.contino.io/insights/platform-engineering 

https://medium.com/contino-engineering/creating-your-internal-developer-platform-part-2-65ff217cecd6 - lists some tools in the space, however, some of the tools that it mentions are things that did not work ve