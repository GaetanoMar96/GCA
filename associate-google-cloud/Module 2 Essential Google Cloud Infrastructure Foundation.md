## Essential Google Cloud Infrastructure: Foundation
### Google Cloud integration

Interacting with google cloud:
•	Cloud console -> GUI
•	Cloud sdk -> cloud cli, storage, bg (command line for bigquery)
•	Api -> you can interact with them
•	Cloud mobile app

Console Cloud vs Cloud shell:
Cloud console keeps track of the context, can use API Cloud to determine which operations are valid and can excute the most frequently activities on its own. Cloud Shell, on opposite, offers a more detailed way, with its commands, to accomplish the tasks.

When you run gcloud on your own machine, the config settings are persisted across sessions. But in Cloud Shell, you need to set this for every new session or reconnection.

Use SSH to perform some admin tasks on the VM. While it is running you can monitor CPU, disk and network load

### Virtual Private Cloud

Projects: key resource to organize resources in GC. A project associate objects and services to billing.
<div align="center">
<img width="1200" src="assets/module_2/projects.png">
</div>

<img width="1200" src="https://user-images.githubusercontent.com/59575502/191574004-3892bf4c-6df8-4010-9190-15dff21945c5.png">
Vpc provide ip addresses for internal and external use.
In an auto mode network, one subnet from each region is automatically created within it. The default network is actually an auto mode network. 
These automatically created subnets use a set of predefined IP ranges with a /20 mask that can be expanded to /16. 
Therefore, as new Google Cloud regions become available, new subnets in those regions are automatically added to auto mode networks using an IP range from that block.
A custom mode network does not automatically create subnets. This type of network provides you with complete control over its subnets and IP ranges. You decide which subnets to create, in regions you choose, and using IP ranges you specify. 
These IP ranges cannot overlap between subnets of the same network.
Now, you can convert an auto mode network to a custom mode network to take advantage of the control that custom mode networks provide. 
However, this conversion is one way, meaning that custom mode networks cannot be changed to auto mode networks. 

Services can communicate with each other and with other on premise services leveraging on VPC.

<img width="1200" src="https://user-images.githubusercontent.com/59575502/191574014-f6b074b9-e304-4901-aa6f-f21c0affaa6b.png">

- Every subnet has ```four reserved IP addresses``` in its primary IP address
- Notice that the **first and second** addresses in the range, .0 and .1, are reserved for the ```network``` and the ```subnet's gateway```, respectively.
- The **second-to-last** address in the range and the **last address**, which are reserved as the ```broadcast``` address.
- Even if 2 VM are in different regions, they can still communicate using the subnet IP, because subnet are cross zones.

### IP Address
An ```ephemeral IP``` address is an IP address that doesn't persist beyond the life of the resource. For example, when you create an instance or forwarding rule without specifying an IP address, Google Cloud automatically assigns the resource an ephemeral IP address.

<div align="center">
<img width="600" src="https://user-images.githubusercontent.com/59575502/191576736-d74621d7-134e-4b80-aa2c-e327dfcf7e85.png">
</div>

<img width="1200" src="https://user-images.githubusercontent.com/59575502/191577028-7422d839-aaae-4b41-ac34-f4023006037d.png">

- GC has 2 type of DNS, Zonal and Global
- If you **delete and recreate** an instance, the ```internal IP``` address can change. This change can disrupt connections from other Compute Engine resources, which must obtain the new IP address before they can connect again.
- DNS name always ```points to a specific instance```, no matter what the internal IP address is. Each instance has a ```metadata server``` that also acts as a ```DNS resolver``` for that instance. The metadata server handles all DNS queries for local network resources and routes all other queries to Google's public DNS servers for public name resolution. 
- Instance is not aware of any ```external IP``` address assigned to it. Instead, the network stores a ```lookup table``` that **matches external IP addresses with the internal IP addresses** of the relevant instances.

<div align="center">
<img width="600" src="https://user-images.githubusercontent.com/59575502/191579043-3fedce69-4c7d-49a0-9a0c-af6a96c708f6.png">
</div>

#### CIDR
```Classless Inter-Domain Routing``` -- also known as ```supernetting``` -- is a method of assigning Internet Protocol (IP) addresses that improves the efficiency of address distribution and replaces the previous system based on Class A, Class B and Class C networks.
#### Alias IP Ranges

<img width="500" align="right" src="https://user-images.githubusercontent.com/59575502/191580492-6223a4c4-303c-4dc9-85e2-158fb0c234ce.svg">

> If you have multiple services running on a VM, and you want to assign a different IP address to each service. You can configure an alias without creating a new network.

All subnets have a primary ```CIDR``` range, which is the range of internal IP addresses that define the subnet. Each VM instance gets its primary internal IP address from this range. You can also allocate alias IP ranges from that primary range, or you can add a secondary range to the subnet and allocate alias IP ranges from the secondary range. Use of alias IP ranges does not require secondary subnet ranges. These secondary subnet ranges merely provide an ```organizational tool```.

- Google publishes the complete list of IP ranges that it announces to the internet in ```goog.json```.
- Google also publishes a list of Google Cloud customer-usable global and regional external IP
addresses ranges in ```cloud.json```.

### Routes and firewall rules
A route is created when a subnet is created. This is what enables VMs on the same network to communicate. 

### [Firewall](https://cloud.google.com/vpc/docs/firewalls)
- Firewall rules let you ```allow or deny``` traffic to and from your VM instances based on a configuration you specify.
- While firewall rules are defined at the ```network level```, connections are allowed or denied on a per-instance basis. You can think of the VPC firewall rules as existing not only between your instances and other networks, but also between individual instances within the same network
- It is ```Global``` like a VPC.
- Existing between instances within same network and instances and other network.
- Each ```VPC``` network acts as a ```distributed firewall``` -> by default it will handle filtering traffic.
> Ex - Applying firewall rules to tagged instances (connections are allowed at per instance basis)

#### Firewall rule is madeup of:
```
gcloud compute
firewall-rules create
http-allow-rule --
direction=INGRESS --
allow=TCP 22 --
target-tags=red-tag
```

- ```direction``` (ingress or egress) -> not both simultaniously
- ```source or destination``` ingress direction -> source is part of IP address, egress direction -> destination is part of IP address
- ```typre of protocol, ports``` (tcp,udp,icmp)
- ```action``` (allow or deny traffic)
- ```priority``` governs the order in which rules are evaluetad
- ```rule assignment``` 
> Each firewall rule can contain either IPv4 or IPv6 ranges, but not both.

Ingress vs egress rules:
Ingress firewall rules protect against incoming connections to the instance from any source. Ingress allow rules allow specific protocol, ports, and IP addresses to connect in. 
Egress firewall rules control outgoing connections originated inside your Google Cloud network. Egress allow rules allow outbound connections that match specific protocol, ports, and IP addresses

You can apply a firewall rule,
- To all instances in a network
- To a specific instances using tags
- Instances using service accounts
> Only supports ```IPV4``` connections. IPv6 connections are also supported in VPC networks that have IPv6 enabled.
cannot share among networks.

When a VM is created the ephemeral external IP address is assigned from a pool. There is no way to predict which address will be assigned, so there is no way to write a rule that will match that VM's IP address before it is assigned. Tags allow a symbolic assignment that does not depend on order in the IP addresses.

### Pricing

<div align="center">
<img width="1200" src="assets/module_2/pricing.png">
</div>


### [Stateful](https://cloud.google.com/vpc/docs/firewalls#specifications)
VPC firewall rules are ```stateful``` -> when a connection is allowed through firewall in either direction, return traffic matching the connection also allowed. You cannot configure a firewall rule to deny associated response traffic.
Return traffic must match the ```5-tuple``` **[source IP, destination IP, source port, destination port, protocol]** of the accepted request traffic, but with the source and destination addresses and ports reversed.

### [Implied rules](https://cloud.google.com/vpc/docs/firewalls#default_firewall_rules)
By default you get implied rules when you create a network.
- Implied allow egress rule
  - 65535 priority
- Implied deny ingress rule
  - 65535 priority
If IPv6 is enabled, the VPC network also has these two implied rules:
- Implied IPv6 allow egress rule.
- Implied IPv6 deny ingress rule

> 65535 is lowest priority so it can be overwrittern.
Google Cloud always allows communication between a VM instance and its corresponding metadata server at ```169.254.169.254```

### [Prepopulated rules](https://cloud.google.com/vpc/docs/firewalls#more_rules_default_vpc)
By default you get prepopulated rules when you create a network.

- ```default-allow-internal``` -> allows all internal traffic
- ```default-allow-ssh``` -> allows ssh internally
- ```default-allow-rdp``` -> connect database internally
- ```default-allow-icmp``` -> ping VMs internally

<div align="center">

[Pricing Docs](https://cloud.google.com/compute/all-pricing#general_network_pricing) | [Pricing Calculator](https://cloud.google.com/products/calculator/)

</div>

### Common network designs
<img width="1200" src="https://user-images.githubusercontent.com/59575502/191584640-76c88b91-d320-4445-8169-3a78438d645c.png">

#### Cloud NAT
> Cloud NAT does not implement inbound NAT.

Cloud NAT is Google's managed network address translation service. It lets you provision your application instances ```without public IP``` addresses while also allowing them to access the Internet in a controlled and efficient manner.

<div align="center">
<img width="600" src="https://user-images.githubusercontent.com/59575502/191584954-3d278870-1118-4f79-8a62-8b7496edda52.png">
</div>


### [Private Google Access](https://cloud.google.com/vpc/docs/private-google-access)

The VPC network has been configured to meet the DNS, routing, and firewall network requirements for Google APIs and services. Private Google Access has been enabled on subnet-a, but not on subnet-b.

- VM A1 can access Google APIs and services, including Cloud Storage, because its network interface is located in subnet-a, which has Private Google Access enabled. Private Google Access applies to the instance because it only has an internal IP address.
- VM B1 cannot access Google APIs and services because it only has an internal IP address and Private Google Access is disabled for subnet-b.
- VM A2 and VM B2 can both access Google APIs and services, including Cloud Storage, because they each have external IP addresses. Private Google Access has no effect on whether or not these instances can access Google APIs and services because both have external IP addresses.

<div align="center">
<img width="600" src="https://user-images.githubusercontent.com/59575502/191585801-0230cb70-3bc6-4be8-9ed2-e75a8281e809.svg">
</div>

---

### [Compute Engine](https://cloud.google.com/compute/docs)
Infrastructure as a service (IaaS)
Predefined or custom machine types:
  - vCPUs (cores) and memory (RAM)
  - Storage
      - Zonal or Regional persistant disk (HDD or SSD)
      - Local SSD
      - Cloud Storage
  - Networking
  - Autoscaling
  - Linux or windows

Tensor Processing Unit, or TPU. TPUs are Google’s custom-developed application-specific integrated circuits (ASICs) used to accelerate machine learning workloads.
TPUs act as domain-specific hardware, as opposed to general-purpose hardware with CPUs and GPUs. This allows for higher efficiency by tailoring architecture to meet the computation needs in a domain, such as the matrix multiplication in machine learning.

Several machine types
● Network throughput scales 2 Gbps per vCPU (small exceptions)
● Theoretical max of 200 Gbps with 176 vCPU 
A vCPU is equal to 1 hardware hyper-thread

Storage: Standard, SSD, or local SSD. So basically, do you want the standard spinning hard disk drives (HDDs), or flash memory solid-state drives (SSDs)? Both of these options provide the same amount of capacity in terms of disk size when choosing a persistent disk. Therefore, the question really is about performance versus cost, because there's a different pricing structure

Networking
● Auto, custom networks
● Inbound/outbound firewall rules
  ○ IP based
  ○ Instance/group tags
● Regional HTTPS load balancing
● Network load balancing
  ○ Does not require pre-warming
● Global and multi-regional subnetworks

<img width="1200" src="https://user-images.githubusercontent.com/59575502/191698038-59fd2dc5-5eba-4482-a684-81ea904be6bc.png">

#### VM Access
- Linux VM (SSH)
    - Require firewall rule to allow tcp:22
    - SSH from console or cloud shell via cloud SDK

- Windows VM (RDP)
    - Require firewall rule to allow tcp:3389
    - Require setting the windows password.

#### VM Lifecycle

<img width="1200" src="https://user-images.githubusercontent.com/59575502/191705232-5d34d8b9-db38-44df-aa11-4a0b835b2f9b.png">

When you define all the properties of an instance and click Create, the instance enters the provisioning state. Here the resources such as CPU, memory, and disks are being reserved for the instance, but the instance itself isn’t running yet. Next, the instance moves to the staging state where resources have been acquired and the instance is prepared for launch. Specifically, in this state, Compute Engine is adding IP addresses, booting up the system image, and booting up the system.
After the instance starts running, it will go through pre-configured startup scripts and enable SSH or RDP access. Now, you can do several things while your instance is running. For example, you can live migrate your virtual machine to another host in the same zone instead of requiring your instance to be rebooted. This allows Google Cloud to perform maintenance that is integral to keeping the infrastructure protected and reliable, without interrupting any of your VMs. While your instance is running, you can also move your VM to a different zone, take a snapshot of the VM’s persistent disk, export the system image, or reconfigure metadata. We will explore some of these tasks in later labs.
Some actions require you to stop your virtual machine; for example, if you want to upgrade your machine by adding more CPU. When the instance enters this state, it will go through pre-configured shutdown scripts and end in the terminated state. From
this state, you can choose to either restart the instance, which would bring it back to its provisioning state, or delete it.
You also have the option to reset a VM, which is similar to pressing the reset button on your computer. This actions wipes the memory contents of the machine and resets the virtual machine to its initial state. The instance remains in the running state through the reset.
The VM may also enter a repairing state. Repairing occurs when the VM encounters an internal error or the underlying machine is unavailable due to maintenance. During this time, the VM is unusable. You are not billed when a VM is in repair. VMs are not covered by the Service level agreement (SLA) while they are in repair. If repair succeeds, the VM returns to one of the above states.
Finally, when you suspend the VM, it enters in the suspending state, before being suspended. You can then resume the VM or delete it.

<img width="1200" src="assets/module_2/vm_options.png">

Availability Policy: 
Called "scheduling options" in SDK/API
  Automatic restart
    ● Automatic VM restart due to crash or maintenance event
      ○ Not preemption or a user-initiated terminate On host maintenance
    ● Determines whether host is live-migrated or terminated due to a maintenance event. Live migration is the default.
    Live migration
    ● During maintenance event, VM is migrated to different hardware without interruption.
    ● Metadata indicates occurrence of live migration.

Pathces:
Managing patches effectively is a great way to keep your infrastructure up-to-date and reduce the risk of security vulnerabilities. 
Use OS patch management to apply operating system patches across a set of Compute Engine VM instances. Long-running VMs require periodic system updates to protect against defects and vulnerabilities.
The OS patch management service has two main components:
  ● Patch compliance reporting, which provides insights on the patch status of your VM instances across Windows and Linux distributions. Along with the insights, you can also view recommendations for your VM instances.
  ● Patch deployment, which automates the operating system and software patch update process. A patch deployment schedules patch jobs. A patch job runs across VM instances and applies patches.

- Create patch approvals. You can select what patches to apply to your system from the full set of updates available for the specific operating system.
- Set up flexible scheduling. You can choose when to run patch updates (one-time and recurring schedules).
- Apply advanced patch configuration settings. You can customize your patches by adding configurations such as pre and post patching scripts.

The image on a stopped VM cannot be changed

#### Create a VM
You can use the Cloud Console, the Cloud Shell command line, or the RESTful API
In the cloud shell you can use the gcloud command. It is better always to start from the console to avoid errors while typing. Vm instances to check the actual VMs running.

Pricing:
- VM are billed in 1 second increments
- The cost is based on machine type. The more CPUs and memory used, the higher the cost.
- Google offers discounts for sustained usage.
- VMs are charged for a minimum of 1 minute of use.
- Preemptible VMs can save you up to 80 percent of the cost of a VM.

Machine family TODO:
- General porpouse most flexible vCPU, cost optimized, day to day computing at low cost.
- 
<div align="center">

#### [Machine types](https://cloud.google.com/compute/docs/machine-types)


<img width="803" src="https://user-images.githubusercontent.com/59575502/191707597-8df8bedd-76de-4611-a991-315414161856.png">
</div>

### Compute VM Options
#### [Preemptible VMs](https://cloud.google.com/compute/docs/instances/preemptible)
A preemptible VM is an instance that you can create and run at a much lower cost than normal instances.
- Low Price
- Terminate at any time
    - preemptible VM only going to live for up to 24 hours (max)
    - 30-second notification before termination (not garanteed)
- No live migrations nor automatic restarts

#### [Spot VMs](https://cloud.google.com/compute/docs/instances/spot)
Spot VMs are the latest version of preemptible VMs.
- Same price as preemptible
- No minimum or maximum run time
- Spot VMs are finite Compute Engine resources, so they might not always be available.
- No live migrations nor automatic restarts

#### [Sole-Tenant Node](https://cloud.google.com/compute/docs/nodes/sole-tenant-nodes)
A ```sole-tenant node``` is a physical Compute Engine server that is dedicated to hosting VM instances only for your specific project. Use sole-tenant nodes to keep your instances physically separated from instances in other projects, or to group your instances together on the same host hardware.

#### [Shielded VMs](https://cloud.google.com/compute/shielded-vm/docs/shielded-vm)
Shielded VMs offer verifiable integrity to your VM instances, so you can be confident that your instances haven't been compromised by boot or kernel-level malware or rootkits.
- secure boot
- virtual trusted platform modules (vTPM)
- Integrity monitoring

#### [Confidential VMs](https://cloud.google.com/compute/confidential-vm/docs/about-cvm#confidential-vm)
Confidential VMs allows you to encrypt data in use, while it's been processed.
- Easy-to-use without making any code changes or having to compromise performance.
- It is a type of ```N2D Compute Engine VM``` instance running on hosts based on the second generation of AMD Epyc processors, code-named "Rome".
- Compute-heavy workloads, with high memory capacity, high throughput, and support for parallel workloads.

#### What is an image?
They are copies of disk contents, used to create VM.

- Boot loader
- OS
- File system structure
- Software
- Customizations

### [Disk Options](https://cloud.google.com/compute/docs/disks)
Every single VM comes with a single root persistent disk because you're choosing a base image to have it loaded on. This image is ```bootable``` in that it can attach to VM and boot from it. It is ```durable``` in that it can survive if the VM terminates. To have a boot disk survive a VM deletion, you need to disable the ```Delete boot disk when instance is deleted``` option in the instance properties
- ```Zonal persistent disk``` -> Efficient, reliable block storage.
- ```Regional persistent disk``` -> Regional block storage replicated in two zones.
- ```Local SSD``` -> High performance, transient, local block storage.
- ```Cloud Storage buckets``` -> Affordable object storage.
- ```Filestore``` -> High performance file storage for Google Cloud users.

<div align="center">
  
[Persistent Disk](https://cloud.google.com/compute/docs/disks#pdspecs) | [Local SSDs](https://cloud.google.com/compute/docs/disks#localssds) | [Cloud Storage buckets](https://cloud.google.com/compute/docs/disks#gcsbuckets) | [Firestore](https://cloud.google.com/firestore/docs)
  
 </div>
 
<img width="1200" src="https://user-images.githubusercontent.com/59575502/191743475-9dbaf893-c426-4930-b165-cb1acd0b5ac1.png">
<img width="1200" src="https://user-images.githubusercontent.com/59575502/191743490-d307ccbb-5d23-4dce-a93a-d3ae15873263.png">
<img width="1200" src="https://user-images.githubusercontent.com/59575502/191743496-8887af29-5d8c-42c8-a0d3-dd502b09e378.png">

### Common Compute Engine actions

<img width="600" align="right" src="https://user-images.githubusercontent.com/59575502/191748781-a88a4009-f89c-4845-8935-804209f3d60d.png">

#### Metadata and Scripts
Every VM instance stores its metadata on a ```metadata server```. The metadata server is particularly useful in combination with startup and shutdown scripts because you can use the metadata server to programmatically get unique information about an instance without additional authorization. 

</br>
<img width="600" align="right" src="https://user-images.githubusercontent.com/59575502/191749860-67e1ca0a-2601-41ca-944c-a17ecdc34b3d.png">
</br>

#### Move a VM into new Zone
For geographical reasons or because a zone has been deprecated you can move a VM even if one of these following scenarios apply,
- The VM instance is in a termination stage 
- The VM instance is a shielded VM that uses UEFI's firmware.
To move your VM, you must shut down the VM, move it to its destination zone or region, and then restart it. After you move your VM, update any references that you have pointing to the original resource, such as any target VMs or target pools that point to the earlier VM.

<img width="600" align="right" src="https://user-images.githubusercontent.com/59575502/191762525-0f0f8767-43d5-427b-bd18-b6b6ba8eba73.png">

##### What is snapshots?
- To backup ```critical data``` into a durable storage solution to meet application, availability, and recovery requirements. 
- These are stored in ```Cloud Storage```
- Snapshots can also be used to ```migrate data between zones```.


