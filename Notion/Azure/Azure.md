Microsoft Azure is a cloud computing platform with set of services(like remote storage, database hosting,…) to help us build solutions to meet our business goals.

> What is cloud computing?

→ CC is the delivery of computing of computing services over the internet.

  

Shared Responsibility Model

It simply means the cloud provider will be responsibe for some responsibilities, not making the consumer responsible for all of them.

The following diagram highlights how the Shared Responsibility Model informs who is responsible for what, depending on the cloud service type.

  

![[shared-responsibility-b3829bfe.svg]]

When using a cloud provider, you’ll always be responsible for:

- The information and data stored in the cloud
- Devices that are allowed to connect to your cloud (cell phones, computers, and so on)
- The accounts and identities of the people, services, and devices within your organization

The cloud provider is always responsible for:

- The physical datacenter
- The physical network
- The physical hosts

Your service model will determine responsibility for things like:

- Operating systems
- Network controls
- Applications
- Identity and infrastructure

  

# Cloud Models

Cloud models define the deployment type of cloud resources. The three main cloud models are: private, public, and hybrid.

## **Private cloud**

Let’s start with a private cloud. A private cloud is, in some ways, the natural evolution from a corporate datacenter. It’s a cloud (delivering IT services over the internet) that’s used by a single entity. Private cloud provides much greater control for the company and its IT department. However, it also comes with greater cost and fewer of the benefits of a public cloud deployment. Finally, a private cloud may be hosted from your on site datacenter. It may also be hosted in a dedicated datacenter offsite, potentially even by a third party that has dedicated that datacenter to your company.

## **Public cloud**

A public cloud is built, controlled, and maintained by a third-party cloud provider. With a public cloud, anyone that wants to purchase cloud services can access and use resources. The general public availability is a key difference between public and private clouds.

## **Hybrid cloud**

A hybrid cloud is a computing environment that uses both public and private clouds in an inter-connected environment. A hybrid cloud environment can be used to allow a private cloud to surge for increased, temporary demand by deploying public cloud resources. Hybrid cloud can be used to provide an extra layer of security. For example, users can flexibly choose which services to keep in public cloud and which to deploy to their private cloud infrastructure.

The following table highlights a few key comparative aspects between the cloud models.

|   |   |   |
|---|---|---|
|**Public cloud**|**Private cloud**|**Hybrid cloud**|
|No capital expenditures to scale up|Organizations have complete control over resources and security|Provides the most flexibility|
|Applications can be quickly provisioned and deprovisioned|Data is not collocated with other organizations’ data|Organizations determine where to run their applications|
|Organizations pay only for what they use|Hardware must be purchased for startup and maintenance|Organizations control security, compliance, or legal requirements|
|Organizations don’t have complete control over resources and security|Organizations are responsible for hardware maintenance and updates||

## **Multi-cloud**

A fourth, and increasingly likely scenario is a multi-cloud scenario. In a multi-cloud scenario, you use multiple public cloud providers. Maybe you use different features from different cloud providers. Or maybe you started your cloud journey with one provider and are in the process of migrating to a different provider. Regardless, in a multi-cloud environment you deal with two (or more) public cloud providers and manage resources and security in both environments.

  

  

# **Consumption-based model**

![[Untitled.png]]

When comparing IT infrastructure models, there are two types of expenses(chi phí) to consider. Capital expenditure (CapEx)(Chi phí vốn) and operational expenditure (OpEx)(Chi phí hoạt động).

CapEx is typically a one-time, up-front expenditure to purchase or secure tangible resources. A new building, repaving the parking lot, building a datacenter, or buying a company vehicle are examples of CapEx.

In contrast, OpEx is spending money on services or products over time. Renting a convention center, leasing a company vehicle, or signing up for cloud services are all examples of OpEx.

**Cloud computing falls under OpEx because cloud computing operates on a consumption-based model**. With cloud computing, you don’t pay for the physical infrastructure, the electricity, the security, or anything else associated with maintaining a datacenter. Instead, you pay for the IT resources you use. If you don’t use any IT resources this month, you don’t pay for any IT resources.

This consumption-based model has many benefits, including:

- ==**No upfront**== costs.
- No need to purchase and manage costly infrastructure that users ==**might not use**== to its fullest potential.
- The ability to ==**pay for more resources**== when they're needed.
- The ability to ==**stop paying**== for resources that are no longer needed.

  

# **Benefits of reliability and predictability in the cloud**

Reliability and predictability are two crucial cloud benefits that help you develop solutions with confidence.

- ==**Reliability**== is the ability of a system to ==**recover from failures**== and continue to function. It's also one of the pillars of the Microsoft Azure Well-Architected Framework.  
    The cloud, by virtue of its ==**decentralized**== design, naturally supports a ==**reliable**== and ==**resilient**==(linh hoạt) infrastructure. With a decentralized design, the cloud enables you to have resources deployed in regions around the world. With this global scale, even if ==**one region has a catastrophic**== event other regions are ==**still up and running**==. You can design your applications to automatically take advantage of this increased reliability. In some cases, your cloud environment itself will automatically ==**shift to a different region**== for you, with no action needed on your part. You’ll learn more about how Azure leverages global scale to provide reliability later in this series.
- ==**Predictability**== in the cloud lets you move forward with confidence. Predictability can be focused on ==**performance**== predictability or ==**cost**== predictability. Both performance and cost predictability are heavily influenced by the Microsoft Azure Well-Architected Framework. Deploy a solution built around this framework and you have a solution whose cost and performance are predictable.
    - Performance predictability focuses on ==**predicting the resources needed to deliver a positive experience for your customers**==. ==**Autoscaling, load balancing, and high availability**== are just some of the cloud concepts that support performance predictability. If you suddenly need more resources, autoscaling can deploy additional resources to meet the demand, and then scale back when the demand drops. Or if the traffic is heavily focused on one area, load balancing will help redirect some of the overload to less stressed areas.
    - Cost predictability is focused on predicting or forecasting the cost of the cloud spend. With the cloud, you can track your resource use in real time, monitor resources to ensure that you’re using them in the most efficient way, and apply data analytics to find patterns and trends that help better plan resource deployments. By operating in the cloud and using cloud analytics and information, you can predict future costs and adjust your resources as needed. You can even use tools like the Total Cost of Ownership (TCO) or Pricing Calculator to get an estimate of potential cloud spend.