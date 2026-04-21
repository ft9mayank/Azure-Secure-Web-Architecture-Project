# Azure Secure Web Architecture Project

## Project Overview

This hands-on Azure infrastructure project was built to simulate a secure web deployment using segmented networking, subnet-level traffic controls, a management access path, and a Standard Public Load Balancer placed in front of a private IIS web server.

This project was developed as an extension of my earlier [Azure Secure Network Lab](https://github.com/ft9mayank/Azure-Secure-Network-Lab), which established the initial jumpbox-based secure access model and private web server architecture.

The goal was to move beyond basic VM deployment and design a more realistic cloud architecture. Instead of assigning a public IP directly to the web server, the environment was built so that the web tier remained private and HTTP traffic was exposed only through the Load Balancer frontend. Administrative access was kept separate through a management VM.

---

## Architecture Snapshot

**Architecture:** Management subnet with jumpbox VM, private web subnet with IIS VM, dedicated application subnet, NSG-based traffic control, and a Standard Public Load Balancer for controlled public access.

---

## Objective

Design and implement a secure Azure web architecture that:

- Uses segmented subnets for management, web, and application tiers
- Restricts administrative access through a management VM
- Keeps the web server private
- Exposes HTTP traffic through a Standard Public Load Balancer
- Applies NSG-based traffic control at the subnet level
- Works within Azure free-tier / low-quota constraints

---

## Project Requirements and Constraints

This project was intentionally designed under realistic limitations:

- Maximum of **2 Windows Server VMs**
- Needed to work within **Azure free-tier / limited vCPU quota**
- Web server should **not** have a direct public IP
- Administrative access should be separated from public web traffic
- Public access should be validated only through the **Load Balancer frontend IP**
- The environment should be documented clearly enough to explain in interviews

These constraints shaped the design and forced the implementation to stay practical, cost-conscious, and architecture-focused.

---

## Core Components

- **Virtual Network:** `vnet-mv-india-01`
- **Subnets:**
  - `snet-mgmt`
  - `snet-web`
  - `snet-app`
- **Network Security Groups:**
  - `nsg-mv-mgmt`
  - `nsg-mv-web`
  - `nsg-mv-app`
- **Virtual Machines:**
  - `vm-mv-jump-01` → management / jumpbox VM
  - `vm-mv-web-01` → private IIS web server
- **Load Balancer:** `lb-mv-web-public`
- **Public IP:** `pip-mv-lb-web`

---

## Traffic Flow Logic

### Administrative Access
1. RDP access is allowed only to the **jumpbox VM**
2. The jumpbox is placed in the **management subnet**
3. Internal administrative access can then be used to manage private resources

### Web Access
1. Users connect to the **public IP of the Standard Load Balancer**
2. The Load Balancer uses an **HTTP health probe** on port 80
3. If the backend is healthy, traffic is forwarded to the private IIS web server in the **web subnet**
4. The web VM itself does **not** require a direct public IP

This approach separates public web access from direct server exposure and creates a stronger security posture than exposing the VM directly.

---

## Security Design

### Segmented Subnets
The environment is split into separate subnets for management, web, and application roles. This improves isolation and simplifies traffic control.

### NSG-Based Access Control
Subnet-level NSGs are used to define and restrict traffic paths:

- `nsg-mv-mgmt` protects the management subnet
- `nsg-mv-web` allows HTTP traffic to the web subnet and restricts administrative access
- `nsg-mv-app` allows RDP only from the management subnet

### Private Web Server
The IIS server runs on a private VM and is publicly accessible only through the Load Balancer frontend.

### Controlled Management Path
Instead of allowing broad direct access, a separate management VM is used as the administrative entry point.

---

## Implementation Summary

### Phase 1: Foundation from Previous Project
This project was built as a direct extension of my earlier **Azure Secure Network Lab**, where I first deployed a secure 2-tier Azure environment with:

- a jumpbox VM in the management subnet
- a private IIS web VM in the web subnet
- subnet segmentation for management and web tiers
- NSG-based access restrictions to control administrative traffic

That earlier project established the secure access model and private backend design. This project builds on that same environment by extending the architecture with an application subnet and introducing a Standard Public Load Balancer to expose the private web tier through a controlled frontend path instead of direct VM exposure.

### Phase 2: Architecture Expansion
This project extended the design by:
- adding an **application subnet**
- creating and associating `nsg-mv-app`
- strengthening subnet-level separation
- preserving the jumpbox-based management model

### Phase 3: Public Web Exposure Through Load Balancer
The web VM remained private while a Standard Public Load Balancer was added with:
- a frontend public IP
- backend pool targeting the web VM NIC
- HTTP health probe on port 80
- load balancing rule for HTTP traffic

### Phase 4: Validation
The environment was validated by:
- confirming IIS worked on the private web VM
- confirming the backend health probe became healthy
- accessing the IIS page successfully through the **Load Balancer public IP**

---

## Outcome

- Successfully exposed a private IIS web server through a **Standard Public Load Balancer**
- Validated backend availability using an **HTTP health probe**
- Preserved subnet segmentation and management-path-based administrative access
- Implemented the environment under **free-tier compute constraints**
- Built a more realistic Azure architecture than a simple single-VM deployment

---

## Screenshots

## 1. Resource Group Overview
Shows the main Azure resources created for the deployment.

![Resource Group Overview](01-resource-group-overview.png)

## 2. VNet and Subnets
Shows the segmented network design with management, web, and application subnets.

![VNet Subnets](02-vnet-subnets.png)

## 3. Web NSG Rules
Shows the NSG rules protecting the web subnet, including HTTP access.

![Web NSG Rules](03-nsg-web-rules.png)

## 4. App NSG Rules
Shows the app subnet restriction model, including RDP access from the management subnet.

![App NSG Rules](04-nsg-app-rules.png)

## 5. Jumpbox VM Overview
Shows the management VM used as the controlled administrative entry point.

![Jumpbox Overview](05-jumpbox-overview.png)

## 6. Web VM Overview
Shows the private IIS web server used as the backend target behind the Load Balancer.

![Web VM Overview](06-web-vm-overview.png)

## 7. Load Balancer Overview
Shows the public Standard Load Balancer that fronts the private web server.

![Load Balancer Overview](07-load-balancer-overview.png)

## 8. Load Balancer Health / Insights
Shows the backend health state and confirms probe success.

![Load Balancer Insights](08-load-balancer-insights.png)

## 9. Public Test Result
Shows the IIS page loading through the Load Balancer public IP.

![Public IP IIS Test](09-public-ip-iis-test.png)

---

## What I Learned

This project helped reinforce the difference between simply deploying VMs and designing actual cloud infrastructure.

Key takeaways:
- why keeping backend systems private is better than assigning public IPs directly
- how subnet segmentation improves security and clarity
- how NSGs shape traffic flow between tiers
- how Azure Load Balancer health probes determine backend availability
- how to build useful projects even under quota and cost constraints

---

## Future Improvements

Planned next steps:
- add **Azure Monitor / Log Analytics**
- expand the application tier further
- export the resource group as **ARM / Bicep**
- add an architecture diagram
- document cost-control strategy for deallocated resources

---

## Resume Bullet Version

Designed and implemented a secure Azure web architecture using segmented management, web, and application subnets, NSG-based traffic controls, a jumpbox administration model, and a Standard Public Load Balancer to expose a private IIS web server under free-tier compute constraints.
