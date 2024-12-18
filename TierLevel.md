## What are Tiers?

A Tier indicates the importance of a service to our company. By classifying services into tiers, we establish a clear hierarchy of criticality. This helps allocate resources effectively and ensure that our most essential services are highly resilient and receive appropriate levels of support and attention.

## How are Tiers Determined?

The importance of a service is assessed by evaluating the potential impact of downtime or failure across three key dimensions:

- **Impact on Revenue:** How directly does service disruption reduce sales, transactions, or overall financial performance?
- **Impact on Customer Trust:** How strongly does the service’s reliability influence customer satisfaction, loyalty, brand perception, and repeat purchases?
- **Impact on Core Operations:** To what extent does downtime impair critical business processes, such as product availability, delivery efficiency, and operational continuity?

## Tier Classifications

- **Tier 1:** Mission-critical services. Failure results in immediate and significant impact on revenue, customer trust, or core operational processes.
- **Tier 2:** Customer-facing services. If disrupted, they degrade the customer experience. While there may be an impact on revenue, operations, or trust, it is more limited compared to Tier 1.
- **Tier 3:** Delay-tolerant services, or internal services. Downtime does not severely or immediately impact the customer experience or critical operations, though it may reduce internal productivity.
- **Tier 4:** Other, or low impact services. Failure does not have an immediate or noticeable effect.

## Why Set Tiers?

Assigning a tier to each service creates transparency and fosters a shared understanding of its importance. This common frame of reference ensures that critical decision-making, resource allocation, and process optimization efforts align with business priorities.

## Examples of Practical Applications of Tiers

- **Service-Level Objectives (SLOs):** Set higher performance and availability expectations (e.g., uptime, response times) for Tier 1 and Tier 2 services that directly affect revenue and trust.
- **Service Maturity Standards:** Encourage Tier 1 services to exemplify best practices in architecture, testing, monitoring, and resilience, setting benchmarks for other tiers to follow.
- **Dependency Management:** By mapping dependencies alongside tier classifications, ensure that services supporting critical functions meet necessary quality and reliability standards.
- **On-Call & Incident Response:** Align incident response times, escalation paths, and staffing levels with the service’s tier. Tier 1 services may require 24/7 on-call support, while lower-tier services have less stringent requirements.
