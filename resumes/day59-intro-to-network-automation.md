# Day 59 - Intro to Network Automation

Network automation provides various benefits:

- Human error is reduced

- Networks become much more scalable (new deployments, network-wide changes and troubleshooting can be implemented in a fraction of time)

- Network-wide policy compliance can be assured (standard configurations software versions, etc.)

- The improved efficiency of network operations reduces the opex (operating expenses) of the network. Each task requires fewer man-hours.

#### What does a router to?
- It forwards messages between networks by examining information in the Layer 3 header.
- It uses a routing protocol like OSPF to share route information with other routers and build a routing table.
- It uses ARP to build an ARP table, mapping IP addresses to MAC addresses.
- It uses Syslog to keep logs of events that occur.
- It allows a user to connect to it via SSH and manage it.

#### What does a switch to?
- It forwards messages within a LAN by examining information in the Layer 2 header.
- It uses STP to enure there are no Layer 2 loops in the network.
- It builds a MAC address table by examining the source MAC address of frames.
- It uses Syslog to keep logs of events that occur.
- It allows users to connect to it via SSH and manage it.

The various functions of network devices can be logically divided up (categorized) into planes:
- Data plane
- Control plane
- Management plane

###  Data plane (aka forwarding plane)

- All tasks involved in forwarding user data/traffic from one interface to another are part of the data plane.
- A router receives a message, looks for the most specific matching route in its routing table and forwards it out the appropriate interface to the next hop.
    - It also de-encapsulates the original layer 2 header and re-encapsulates with a new header destined for the next hop's MAC address.

- A switch receives a message, looks at the destination MAX address, and forwards it out of the appropriate interface (or floods it).
    - This includes functions like adding or removing 802.1q VLAN tags

- NAT (changing the src/dst addresses before forwarding) is part of the data plane

- Deciding to forward or discard messages due to ACLs, port security, etc. is part of the data plane.

- The data plane is the reason we buy routers and switches (and network infrastructure in general), to forward messages. However, the Control plane and Management plane are both necessary to enable the data plane to do its job.

- ASIC (Application-Specific Integrated Circuits) are used, because CPU processing is slow.
    - When a frame is received the ASIC is responsible for the switching logic
    - The MAC address table is stored in a kind of memory called TCAM (Ternary Content-Adressable Memory) -> allows for very fast lookups
    - The ASIC feeds the destination MAC address of the frame into the TCAM, which returns the matching MAC address table entry (aka CAM table entry)


### Control Plane

- How does a device's data plane make its forwarding decision?
    - routing table, MAC address table, ARP table, STP, etc.

- Functions that build these tables (and other functions that influence the data plane) are part of the control plane.

- The control plane controls what the data plane does, for example, by building the router's routing table.

- The control plane performs overhead work.
    - OSPF itself doesn't forward user data packets, but it informs the data plane about how packets should be forwarded.
    - STP itself isn't involved in the process of forwarding frames, but it informs the data plane about which interfaces should and shouldn't be used to forward frames.
    - ARP messages aren't user data, but they are used to build an ARP table which is ussed in the process of forwarding data.

---

- **In traditional networking the data plane and control plane are both distributed. Each device has its own data plane and its own control plane. The planes are "distributed" throughout the network.** => this is different from SDNS

### Management Plane

- Like the control plane, the management plane performs overhead work.
    - However, the management plane doesn't directly affect the forwarding of messages in the data plane.

- The management plane consists of protocols that are used to manage devices.
    - SSH/Telnet, used to connect to the CLI of a device to configure/manage it.
    - Syslog, used to keep logs of events that occur on the device.
    - SNMP, used to monitor the operations and status of the device.
    - NTP, used to maintain accurate time on the device. 


---

- When a device receives control/management traffic (destined for itself), it will be processes in the CPU.
- When a device receives data traffic which should pass through te device, it is processed by the ASIC for maximum speed.

## Software-Defined Networking

- **Software-Defined Networking (SDN)** is an approach to networking that centralizes the control plane into an application called a controller.
- SDN is also called **Software-Defined Architecture (SDA)** or **Controller-Based Networking**.
- Traditional control planes use a distributed architecture (e.g. each router in the network runs OSPF and the routers share routing information and then calculate their preferred routes to eac destination).
- An SDN controller centralizes control plane functions like calculating routes.
    - How much of the control plane is centalized can vary greatly.
- The controller can interact programmatically with the network devices using APIs (Application Programming Interface).
    

### Southbound Interface (SBI)

- The SBI is used for communications between the controller and the network devices it controls.
- It tipycally consists of a communication protocol and API (Application Programming Interface).
- API faciltates data exchanges between programs.
    - Data is exhanged between the controller and the network devices.
    - An API on the network devices allows the controller to access information on devices. control their data plane tables, etc.
- Some examples of SBIs:
    - OpenFlow
    - Cisco OpFlex
    - Cisco onePK (Open Network Environment Platform Kit)
    - NETCONF

- Using the SBI, the controller communicates with the managed devices and gathers information about them:
    - The devices in the network.
    - The topology (how the devices are connected together).
    - The available interfaces on each device.
    - Their configurations.

### Northbound Interface (NBI)

- The **Northbound Interface (NBI)** is what allows us to interact with the controller, access the data it gathers about the network, program it, and make changes in the network via the SBI.
- A *REST API (Represenational State Transfer)* is used on the controller as an interface for apps to interact with it.
- Data is sent in a structured (serialized) format such as JSON or XML.
    - This makes it much easier for prorams to use the data.

## Automation in Traditional Networks vs SDNs

- Networking tasks can be automated in traditional networks:
    - Scripts can be written (e.g. Python) to push commands to many devices at once.
    - Python with good use of Regular Expressions can parse through `show` commands to gather information about the network devices.
- However, the **robust and centralized data collected by SDN controllers greatly facilitates** this functions.
    - The controller collects information about all devices in the network.
    - Northbound APIs allow apps to access information in a format that is easy for programs to understand (ie. JSON, XML).
    - The centralized data facilitates network-wide analytics.
- SDN tools can provide the benefits of automation without the requirement of 3rd-party scripts & apps.
    - You don't need expertise in automation to make use of SDN tools.
    - However, APIs allow 3rd-party applications to interact with the controller, which can be very powerful.

- Although SDN and automation aren't the same thing, the SDN architecture greatly facilittes the automation of various tasks in the network via the SDN controller and APIs.