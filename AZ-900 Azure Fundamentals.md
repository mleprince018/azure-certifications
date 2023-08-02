# Azure Fundamentals AZ-900 # 

# Cloud Concepts 

## Basics
- **cloud computing**: delivery of computing services over the internet 
    - common infra (VMs, storage, db, networking, etc... now expanded to include AI, ML...)
    - virtually unlimited resources in cloud provider's data center 
    - compute power & storage are main things offered by external data center - backups, OS updates...
- **describe shared responsibility model** 
    - Cloud provider is ALWAYS responsible for: 
        - physical hosts
        - physical network 
        - physical datacenter 
    - PaaS: OS owned by datacenter - network/applicaiton & AD is a mix 
    - SaaS: AD is a mix, but applications, network, OS, hosts etc all owned by cloud provider 
    - client is ALWAYS responsible for: 
        - info & data stored in the cloud 
        - devices that are allowed to connect to your cloud 
        - accounts & IDs of people/services/devices in cloud 
    - Depending on what you select, these services can change:
        - OS
        - Network controls 
        - applications 
        - ID & AD Infra 
- **public/private/hybrid clouds**
    - private cloud has startup costs, own all data/security/maintenance... 
    - public you can offload these costs 
    - hybrid provides flexibility, but adds complexity 
    - multi-cloud - use multiple cloud environments 
    - Azure Arc: manage cloud envm in Azure or private data center or hybrid config 
    - Azure VMware: allows you to run VMware workloads in Azure with seamless integration & scalability 
- use-cases for each cloud
- **consumption based model** 
    - CapEx vs OpEx 
        - Cloud computing is OpEx because (in theory) your costs will scale up & down with your org's operations 
        - no upfront costs, don't pay for resources when you don't need them or aren't using them... (in theory) no waste or excess capacity 
    - Cloud is a pay-as-you-go 
        - plan and manage your operating costs 
        - run infra more efficiently
        - scale as your business needs to change 
        - rent your infra s you need it, cloud provider will handle the rest 

## Key Concepts 
- **HA & Scalability** 
    - High Availability (HA): ensuring maximum availability - often done through uptime guarantees as part of SLA 
    - Azure SLA: service level agreement - agreement between
        - % of uptime: a service or application's availability - and what happens when uptime breaks (often monetary payment to make amends) 
        - often 99% & up - requires extra cost & often duplicate infra, etc and the more 9s added generally adds more cost 
    - Scalability: ability to adjust resources to meet demand 
        - grants flexibility to be "waste-free" when allocating compute/storage etc...
        - vertical scaling: increasing/decreasing machine resources 
        - horizontal scaling: adding/removing extra machines 
- **Realiability & Predictability** 
    - Realiability: ability of a system to recover from failures & continue to function 
        - Decentralizing allows for a reliable & resilient infra 
    - Predictability: cost/price predictability & performance predictability to confidently move business forward 
        - Performance predictability: autoscaling, load balancing, HA allow you to grow/shrink resources to meet demand and provide consistent exp to users 
        - Cost predictability: track resource usage in RT, forecast spend, optimize spend, etc... 
- **Security & Governance** 
    - Cloud has features to support governance & compliance
        - establishing templates to meet common corp/regulatory standards (can auto-update) 
        - auditing - to flag resources out of compliance & basic mitigation strategies 
    - can meet security needs: can take control of OS, patching and maintenance or defer that to cloud provider 
        - can handle DDoS
- **Manageability** 
    - Cloud Management capabilities: 
        - management of cloud to autoscale 
        - auto deploy based on preconfigured templates 
        - monitor health of resources & auto replace failing ones 
        - receive alerts & metrics 
    - You can take action to manage these through: web portal, CLI, APIs or powershell 

## IaaS, PaaS & SaaS ## 
- **IaaS Infrastructure as a Service** 
    - most flexibile and provides user with most amount of control (network, ID/AD, OS, install, config, maintenance, patching, updates, sescurity...)
        - you are literally renting hardware from cloud provider and doing the rest 
    - Makes the most sense for: 
        - Lift n shift migration: Get your applications/services/etc running in the cloud by copy-pasting it there 
        - Testing & development: stand up/shut down environments, builds as needed 
- **PaaS Platform as a Service** 
    - Middle ground - where licensing/patching/updates for OS & Databases are taken care of 
    - Makes the most sense for: 
        - Development framework: complete a dev environment without all dev infra -- reference HA databases so developer doesn't have to maintain an HA database, just code against one 
        - Analytics/Business Intelligence: tools provided with PaaS to allow orgs to analyze & mine their data, finding insights/patterns for various org uses and business purposes 
- **SaaS Software as a Service**
    - most complete software service - essentially renting a fully developed app (salesforce) 
        - places most resp on cloud owner and least on client 
        - client is basically only responsible for data they put in, the devices that connect & the users they add 
    - Makes the most sense for: email/messaging, business productivity applications, finance & expense tracking 

![Shared Responsibility Model](./pictures/Fundamentals_shared-responsibility.svg)


# Azure Architecture & Services 

## Core Components 
- Physical org of Azure - Datacenters, availability zones & regions 
- Logical/Management structure of azure - resource groups, subscriptions & management groups 
- selection of services 
    - azure web apps
    - azure functions - serverless (FaaS)
    - K8s services 
    - DBs on demand 
    - Cosmos DB - noSQL db ~ dynamodb from amazon? 
- Azure cloud is continously improved and offerings grow, you can build on your terms in your language/framework/globally 
    - allows you to opearte hybrid, public cloud, security from the ground up 
- Over 100 services, clients typically start with VMs, but you can expand to variety of services - AI/ML, etc... 

### Azure Accounts
- **Azure Account**: Top level parent/root
- **Subscriptions**: (development/marketing/sales...) ?groupings within an account? 
- **Resource Groups**: group resources together
    - **Resources**: Azure services and objects 

- free account includes 12 months access for free to 25 products, a credit to use within 1 month 
- Azure student grants 12 months free for certain software dev tools and a credit to use within 12 months 
- Learn exercises use a temp subscription called a sandbox that will clean up resources after you've completed the exercise 

![Account Scope Groupings](./pictures/Fundamentals_account-scope-groupings.png)

### Physical Infra - Regions & AZs 
- Physically Azure is a set of datacenters spread across the globe 
    - just like in a corporate datacenter, they are large facilities filled with racks of servers/cpus/storage with dedicated power, cooling and networking infra 
- **Region**: geographical area that contains at least one datacenter(s) that are nearby and networked with a low-latency network 
    - Azure assigns & controls resources within each region to ensure workloads are appropriately balanced 
    - When you deploy a resource - you choose a region to put it in  
        - NOTE: certain services and types of services (storage, VM sizes, etc...) are only available in certain regions, others are globally standard 
- **Availability Zones**: physically separate datacenters within an Azure region 
    - one or more datacenters equipped with indp power, cooling and network... 
    - It's an isolation boundary should 1 zone go down, the other continues working --> Azure works to have 3 AZs in each region for full HA/availability support. 

#### Ensuring Availability with AZs & Regions 
- setting up redundancy originally required duplicate hardware environments, now you can just click through different AZs 
    - you can colocate across AZs or replicate data to backup in a different AZ 
- **Zonal Services**: you pin the resource to a specific zone (VMs, Managed Disks, IP addresses...)
- **Zone-redundant services**: platform auto replicates across zones (zone redundant storage, SQL db...)
- **Non-regional Services**: services always available and are resilient to zone-wide outages & region-wide outages. 
- **Region Pairs**: most azure regions are paired with another region at least 300 miles away for DR purposes 
    - West US paired with East US region to be resilient to a west coast disaster or east coast disaster. 
        - NOTE: data replication across regions is not always automatic and may need to be manually configured 
    - if extensive outage occurs, one region out of every pair is prioritized for restore 
    - updates are rolled out to one region within a pair at a time to minimize downtime/risk 
    - data resides within same geography (to comply with tax/law enforcement rules) 
- **Sovereign Regions**: Azure instances issolated from main instance often for governmental purposes
    - US DoD Central, US Gov Virginia, US Gov Iowa and others 

### Management Infra - Resources & Resource Groups & Subscriptions and Management Groups
- Accounts --> Subscriptions --> Resource Groups --> Resources 
- **Resource**: the building block of Azure, anything you create, provision & deploy  
- **Resource Groups**: Groupings of resources - any resource must exist within a resource group. 
    - Each resource can only be in one resource group at a time (if you take it out of Resource Group A and put it into B, it is no longer a part of A)
    - Resource Groups cannot be nested 
    - with the above restrictions - resource groups should be designed carefully: a dev environment under 1 entire resource group to be easily provisioned or removed - or a 3 schema compute engine spread across 3 RGs... 
    - Resource Groups Auto-inherit settings from subscriptions, and resources inherit settings from resource groups. (for current & future resources)

- **Subscriptions**: a unit of management, billing and scale - just how resource groups organize resources, subscriptions allow you to organize your resource groups and facilitate billing
    - Links to an Azure account which is an ID in Azure AD or directory/domain which azure trusts --> this allows you to provide an authenticated & authorized access to provision & use azure products/services. 
- Subscription Boundaries: configure different billing models and/or different access controls 
    - *Billing boundary*: determines how an Azure account is billed for - can create multiple subscriptions for different billing reqs with different invoices/reports to allow you to organize & manage costs 
    - *Access control boundary*: access-mgt policies applied at subscription level to reflect org structure and billing to different departments or set policies at dept levels 
        - limit marketing to small VMs for example 
- Reasons to create separate subscriptions: 
    - Environments - dev/test/prod costs & access control set at subscription level 
    - Org Structures - subscriptions to follow org structure and grant/deny access to org structure 
    - Billing - create subscriptions for billing levels - prod vs dev workloads 

- **Management Groups** : Groupings of subscriptions 
    - All subscriptions within a management group auto-inherit conditions applied to management group
    - allow for enterprise-grade management at scale 
        - hierarchy that applies a policy (limit VM locations to US West Region for PROD, and US East for DR)
        - provide user access to multiple subscriptions (allows for single RBAC to grant multi subscription access - rather than multi-RBAC per user)
    - Capabilities: CAN be nested 
    - Restrictions: a tree structure that goes 6 deep (not including head) and doesn't allow one branch to have 2 parents, max 10k mgt groups under a single directory   

![Management Groups & Subscriptions](./pictures/Fundamentals_management-groups-subscriptions.png) 

## Compute & Networking Services 
### Virtual Machines in Azure
- VMs are a basic unit of IaaS and are abstraction of core, ram, & storage making it very versatile. Use when you need: 
    - total control over OS 
    - ability to run custom software 
    - use custom hosting configs 
- VMs provide flexibility of infra without OS/software updates and maintenance 
    - can create/provision VM in minutes using images
- **VM Scale Sets** 
    - VM scale creates a group of identical, load-balanced VMs 
        - can be configured to auto increase/decrease with demand - or scale based on schedule 
        - great for variable compute, big data, container workloads 
    - MUCH easier than manually setting up *N* number of VMs individually and configuring individually 
- **VM Machine Availability Sets**
    - VMs stagger updates and have varied power/network connectivity preventing loss of VMs during catastrophic event 
    - Can group VMs in 2 ways: 
        - *Update Domain*: VMs that can be rebooted at the same time, can apply updates in a roll-out fashion, and given 30 min to recover before next group begins updates
        - *Fault domain*: groups VMs by common power source & network switch - by default, it will split across 3 fault domains (? is this the same as AZ?) 
    - you only pay for VM instances you create - not using this Availability auto-setup 
- Usages for VMs: 
    - Testing & Development: quick * easy way to create different OS & app configs, easy delete when done 
    - Running apps in the cloud: applications can be started/stopped when needed to limit infra costs 
    - Extending datacenter into the cloud: adding a VNet in Azure and adding VMs to that VNet 
    - During DR - can create VMs in Azure as a DR strategy 
    - Assist with lift & shift strategy into the cloud 
- VM Resources - additional azure objects/resources tied to VM that are necessary for it to function
    - Size of VM (purpose, # of cores, RAM)
    - Storage (Hard drives, SSD, etc)
    - Networking (VNet, Public IP, Port config...)

### Virtual Desktop 
- A desktop & app virtualization service that runs in the cloud - enables you to use a cloud-hosted version of windows from any location 
    - works across devices, OS, and apps/browsers everywhere 
    - centralized RBAC with Azure AD, and can enable multi-factor authc 
    - can separate your data/apps from local hardware (physical laptop) 
- Multi-session Windows 10 or 11: enables concurrent users on a single VM 

### Azure Containers 
- To run multiple apps on a single host machine requires extensive app configs or multiple VMs 
    - can do that more easily with containers
    - less duplication of OS/VM operations 
    - containers virtualize the OS 
    - Portability: containers can be used to ensure consistency across deployments - as VMs between dev/prod can have different configs, etc... 
- VMs have an OS you can connect to and manage - containers don't allow you to config the OS and treat the container as a "unit" 
    - this allows you to build/deploy/test/delete containers rather than an app and its various configs
- Azure container instances - allow you to run through their containers in a PaaS
    - primarily used with microservices architecture with small, independent pieces that can be split off and run as containers & individually scale up/down services as needed

### Azure Functions
- FaaS (serverless) event based, reactive applications 
- all you need is a bit of code or particular process to run when a certain event occurs. 
    - you are only charged for CPU time when function runs 
- Stateless functions are default (restart anew with no memory of the past for every request, and treat them all the same)
- Stateful (Durable Functions)  context is passed through the function to track prior activity 

### App hosting options 
- Beyond VMs & Containers - Azure App Service is a great alternative to hosting apps 
- Can build and host web apps, background jobs, mobile backends and RESTful APIs in programming language of choice without managing infra 
    - all with auto-scaling and HA for Windows/Linux
    - enables automated deployments from Github, azure or other git repos 
- robust hosting option to build and maintain app, while Azure keeps infra running 
- Hosts common apps like: 
    - Web Apps 
        - ASP, .NET, Java, Ruby, Node.js PHP or python
    - API Apps 
    - WebJobs 
        - run a program or script in same context as webapp, etc - scheduled or run by trigger 
    - Mobile Apps 
        - authenticate users, store data, send notifications, execute custom logic 
- App Service handles most of the infra decisions you deal with in hosting web-accessible apps 
    - deployment and management are integrated into platform 
    - endpoints can be secured
    - scalable to handle dynamic demand with built in load balancing and traffic manager to provide HA 


### Virtual Networking 

### Public/Private Endpoints 

## Storage Services 

## Access Controls, Identiy & Security 

# Azure Management & Governance 

 
