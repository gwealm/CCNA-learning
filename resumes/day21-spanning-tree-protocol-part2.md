# Day 20 - Spanning Tree Protocol (Part 2)

## STP Port States

| STP Port State | Stable / Transitional |
| -------------- | --------------------- |
| **Blocking**   | Stable                |
| **Listening**  | Transitional          |
| **Leaning**    | Transitional          |
| **Forwarding** | Stable                |
| **(Disabled)** | ---------             |

-   **Root/Designated** ports remain stable in a **Forwarding** state.
-   **Non-Designated** ports remain stable in a **Blocking** state.

-   **Listening** and **Learning** are transitional states which are passed through when an interface is activated, or when a Blocking port must transition to a Forwarding state due to a change in the network topology.

#### Blocking

-   Non-designated ports are in a **Blocking** state.
-   **Interfaces** in a Blocking state are effectively **disabled to prevent loops**.
-   **Interfaces** in a Blocking state **do not send/receive regular network traffic**.
-   **Interfaces** in a Blocking state **receive STP BPDUs**.
    -   They need to be aware of the overall Spanning Tree to be ready to change state if necessary.
-   **Interfaces** in a Blocking state **do not forward STP BPDUs**.
-   **Interfaces** in a Blocking state **do not learn MAC addresses**.

#### Listening

-   **After the Blocking state**, **interfaces with** the **Designated** or **Root role** **enter** the **Listening State**.
-   **Only Designated or Root ports** enter the Listening state (Non-designated ports are always Blocking).
    -   This is because the Listening State is a transition state that eventually leeds to the Forwarding State.
-   The Listening state is **15 seconds long by default**
    -   This is **determined by the Forward delay timer**
-   An interface in the Listening state **ONLY forwards/receives STP BPDUs**.
-   An interface in the Listening state **does NOT send/receive regular traffic**.
-   An interface in the Listening state **does not learn MAC addresses** **from regular traffic** that arrives on the interface.

#### Learning

-   **After the Listening State**, a **Designated or Root** **port** will enter the **Learning state**.
-   The Listening state is **15 seconds long by default**
    -   This is **determined by the Forward delay timer**
-   An interface in the Leaning state **ONLY forwards/receives STP BPDUs**.
-   An interface in the Leaning state **does NOT send/receive regular traffic**.
-   An interface in the Leaning state **learns MAC addresses** **from regular traffic** that arrives on the interface.

#### Forwarding

-   **Root and Designated Ports** are **in Forwarding state**.
-   A port in the Forwarding state **operates as normal**.
-   A port in the Forwarding state **sends/receives BPDUs**.
-   A port in the Forwarding state **sends/receives regular traffic**.
-   A port in the Forwarding state **learns MAC Addresses**.

### Port States Review

| STP Port State | Send/Receive BPDUs | Frame forwarding (regular traffic) | MAC Address Learning | State/Transitional |
| -------------- | ------------------ | ---------------------------------- | -------------------- | ------------------ |
| **Blocking**   | NO/YES             | NO                                 | NO                   | Stable             |
| **Listening**  | YES/YES            | NO                                 | NO                   | Transitional       |
| **Learning**   | YES/YES            | NO                                 | YES                  | Transitional       |
| **Forwarding** | YES/YES            | YES                                | YES                  | Stable             |
| **Disabled**   | NO/NO              | NO                                 | NO                   | Stable             |

## Spanning Tree Timers

| STP Timer         | Purpose                                                                                                          | Duration            |
| ----------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------- |
| **Hello**         | How often the root bridge sends hello BPDUs                                                                      | 2sec                |
| **Forward delay** | How long the switch will stay in the Listening and Learning states (each state is 15 seconds = total 30 seconds) | 15 sec              |
| **Max Age**       | How long an interface will wait after ceasing to receive Hello BPDUs to change the STP topology                  | 20 sec (10\* hello) |


#### Hello Timer

- The **switches** will **only forward BPDUs on their designated ports**.
    - They do not forward the BPDUs out of their root ports and non-designated ports

#### Max Age Timer

- If another BPDU is received before max age timer counts down to 0, the time will reset to 20 seconds and no changes will occur.
- If another BPDU is not received, the max age timer counts down to 0 and the switch will reevaluate its STP choices, including root bridge, and local root, designated and non-designated ports.
- If a non-designated port is selected to become a designated or root port, it will transition from the blocking state to the listening state (15 seconds), learning state (15 seconds), and then finally the forwarding state. So, it can take a total of 50 seconds for a blocking interface to transition to forwarding.
- These timers and transitional states are to make sure that loops aren't accidentally created by an interface moving to forwarding state too soon.

- **NOTE:** A forwarding interface can move directly to a blocking state (there is no worry about creating a loop by blocking an interface).

## STP BPDU

![STP BPDU](assets/day21/stp-bpdu.png)

- **PVST**: Only ISL trunk encapsulation
- **PVST+**: Supports 802.1Q

- **L2 Dst** in Regular STP (not Cisco's PVST+) is always set to **0180.c200.0000**. 
- **L2 Dst** in PVST+ is always set to **01:00:0c:cc:cc:cd**. 


#### STP BPDU Fields

- **Protocol Identifier**: always set to 0x0000 for STP 
- **Protocol Version Identifier**: set to 0 to Spanning Tree
    - RSTP will have a different value
- **BPDU Type**: 0x00 for configuration mode (not really relevant for CCNA)
- **BPDU Flags**: used to signal topology changes to other switches
- **Root Identifier**
    - **Root Bridge Priority**
    - **Root Bridge System ID Extension** (vlan ID)
    - **Root Bridge System ID** (mac addr of the root bridge)
- **Root Path Cost**
- **Bridge Identifier**
    - **Bridge Priority**
    - **Bridge System ID Extension** (vlan ID)
    - **Bridge System ID** (mac addr of the root bridge)
- **Port Identifier**: the interface which send the BPDU in hex (e.g. 0x8002 => 0x80 is 128 which is the default port priority and 0x02 is the port itself)
- **Message Age**: starts at 0 at the root bridge and is increased by 1 each time it is forwarded by another switch. It is subtracted from the max age when a switch receives the BPDU.
    - E.g. if a BPDU is passed by 5 switches, when it reaches the 6th switch it will immediatly reduce its max age timer to 15 instead of 20.
- **Max Age**
- **Hello Time**

## Spanning Tree Optional Features (STP Toolkit)

### Portfast

Portfast allows a port to move immediately to the Forwarding state, **bypassing Linstening and Learning** states,
If used, it must be enabled only on ports connected to end-hosts.
If enabled on a port connected to another switch it could cause a Layer 2 Loop.

- `spanning-tree portfast` enables PortFast
    - PortFast will only have effect when the interface is in a non-trunking mode.

- You can also enable portfast with the following command: `spanning-tree portfast default`
    - This enables portfast on all access ports (not trunk ports).

- **Danger:** But what if an employee plugs another switvch into the network connected to two ports using PortFast, therefore causing a loop? (answer: BPDU Guards)

### BPDU Guard

- **If an interface with BPDU Guard enabled receives a BPDU from another switch**, the **interface will be shut down** to prevent a loop from forming.

- (in interfce configuration mode) `spanning-tree bpduguard enable` enables BPDU on an interface.

- You can also enable BPDU Guard with the following command: `spanning-tree portfast bpduguard default`

- To enable a port that was disabled by BPDU Guard, simply `shutdown` and then `no shutdown` the interface.
    - Note that BPDU Guard will still be enabled, so, if the problem is not solved, the same problem will arise.

#### Root Guard

- If you enable **root guard** on an interface, even if it receives a superior BPDU (lower bridge ID) on that interface, the switch will not accept the new switch as the root bridge. The interface will be disabled.


#### Loop guard

- If you enable **loop guard** on an interface, even if the interface stops receving BPDUs, it will not start forwarding. The interface will be disabled.

## STP Configuration

```
SW1(config)# spanning-tree mode ?
    mst         Multiple spanning tree mode
    pvst        Per-Vlan spanning tree mode
    rapid-pvst  Per-Vlan rapid spanning tree mode

SW1(config)# spanning-tree mode pvst

%% Setting a switch to the root bridge
SW1(config)# spanning-tree vlan 1 root primary
SW1(config)# do show spanning-tree

VLAN0001
    Spanning tree enabled protocol ieee
    Root ID     Priority    2457
                Address     cccc.cccc.cccc
                This bridge is the root
                Hello Time  2 sec   Max Age 20 sec  Forward Delay 15 sec

    Bridge ID   Priority    2457 (priority 24576 sys-id-ext 1)
                Address     cccc.cccc.cccc
                This bridge is the root
                Hello Time  2 sec   Max Age 20 sec  Forward Delay 15 sec

SW1(config)# spanning-tree mode pvst
```

- The `spanning-tree vlan <vlan-number> root primary` command sets the STP priority to 24576. If another switch already has a priority lower than 24576, it sets this switch's priority to 4096 less than the other switch's priority.
    - In the above case, if you check the running-config, you can check that the command that is actually applied in `spanning-tree vlan 1 priority 24576`


```
SW1(config)# spanning-tree vlan 1 root secondary
SW1(config)# do show spanning-tree

VLAN0001
    Spanning tree enabled protocol ieee
    Root ID     Priority    2457
                Address     cccc.cccc.cccc
                Cost        4
                Port        1 (GigabitEthernet0/0)
                This bridge is the root
                Hello Time  2 sec   Max Age 20 sec  Forward Delay 15 sec

    Bridge ID   Priority    2457 (priority 24576 sys-id-ext 1)
                Address     bbbb.bbbb.bbbb
                This bridge is the root
                Hello Time  2 sec   Max Age 20 sec  Forward Delay 15 sec
                Aging Time  300 sec

SW1(config)# spanning-tree mode pvst
```

- Secondary root bridge will be the next in line to be the root bridge if the current one fails. 

- Like in the other example, the command that is actually applied is `spanning-tree vlan 1 priority 28672`

- The spanning tree root command is just "**syntatic sugar**" for setting the priority without manually having to put increments of 4096.


### Load Balancing

You can load balance traffic by setting up different primary and secondary switches on different VLANs, so that different connections are used on each.


### Configuring STP Port Setting

- There are 2 VLAN settings that can be configured (cost and port-priority) and they are configured on a per-vlan basis.

```
SW2(Config-if)# spanning-tree vlan 1 ?
    cost            Change an interface's per VLAN spanning tree path cost
    port-priority   Change an interface's spanning tree por priority

SW2(config-if)# spanning.tree vlan 1 cost 200

% Port priority needs to be set in increments of 32
SW2(config-if)# spanning.tree vlan 1 port-priority 32   
```