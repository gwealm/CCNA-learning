# VLANS - Part 3

## Native VLAN on a router (ROAS)

- There are **2 methods** of configuring the native VLAN on a router:
    - Use the command `encapsulation dot1q <vlan-id> native` on the router subinterface

    - Configure the IP address for the native VLAN on the router's physical interface (the `encapsulation dot1q <vlan-id>` is not necessary )

## Layer 3 (Multilayer) Switches

- A multilayer switch is **capable of both switching and routing**.
- It is **"Layer 3 Aware"**.
- You can **assign IP addresses to its interfaces**, like a router.
- You can **create virtual interfaces for each VLAN**, and **assign IP addresses to those interfaces**.
- You can configure routes on it, just like a router.
- It **can be used for inter-VLAN routing**.
    - using 1 connection for each vlan works, but if you have many vlans you probaby won't have enough interfaces on the router.
    - using ROAS (uses a single trunk connection which carries all the traffic from the switch to the router) is efficient in terms of the number of interfaces, but in a busy network, all of the traffic going from the router to the switch can cause congestion.

### Inter-VLAN Routing via SVI

- **SVIs (Switch Virtual Interfaces)** are the virtual interfaces you can assign IP addresses to in a multilayer switch.

- Configure each PC to use the SVI (not the router) as their gateway address.

- To send the traffic to different subnets/VLANs, the PCs will send traffic to the switch, and the switch will route the traffic.

```
%% Remove the ROAS config %%
SW2(config)# default interface g0/1

%% This command enables layer 3 routing on switch
SW2(config)# ip routing

SW2(config)# interface g0/!
SW2(config-if)# no switchport
SW2(config-if)# ip address ....

%% Configuring Switch Virtual Interfaces (SVIs)
SW2(config)# interface vlan10
SW2(config-if)# ip address 192.168.1.62 255.255.255.192
SW2(config-if)# no shutdown
```

- `ip routing` enables layer 3 routing on the switch
- `no switchport` configures the interface as a "**routed port**" (Layer 3, not a Layer 2/switchport)
    - after this you are able to assign an ip address to it
- `show interfaces status` to display the interfaces
    - The **VLAN** column will appear as **routed**


- **SVI's are shutdown by default**, so remember to use `no shutdown` to enable them.

### Conditions for an SVI to be UP/UP

1) The **VLAN must exist** on the switch
    - When you assign an access port to a VLAN, if the VLAN doesn't exist already on the switch, the switch will automatically create the VLAN. However, **if you create an SVI for a VLAN that doesn't exist yet**, the **switch will not automatically create the VLAN**.

2) The switch must have **at least one access port in the VLAN in an up/up state**, AND/OR **one trunk port that allows the VLAN that is in an up/up state**.

3) The **VLAN must not be shutdown** (you can use the **shutdown** command to disable a VLAN)

4) The **SVI must not be shutdown** (SVIs are disabled by default)

#### Check the mode of an interface

`show interfaces <interface> switchport`