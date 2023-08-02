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

## Compute & Networking Services 

## Storage Services 

## Access Controls, Identiy & Security 

# Azure Management & Governance 

 
