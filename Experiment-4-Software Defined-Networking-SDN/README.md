# Assignment 4: Software-Defined Networking (SDN)

## Objective
To implement and test an SDN environment using Mininet, Open vSwitch (OVS), and the Ryu Controller.

## Tools & Technologies
- Ubuntu Linux
- Mininet
- Open vSwitch
- Ryu Controller
- OpenFlow 1.3
- Python 3.11

## Network Topology

```text
        Ryu Controller
              |
         Open vSwitch
              s1
          /   |   \
         h1   h2   h3
```

## Implementation Procedure

### 1. Start Ryu Controller

```bash
ryu-manager ryu.app.simple_switch_13
```

### 2. Create Mininet Topology

```bash
sudo mn --topo single,3 --controller=remote --switch ovsk
```

### 3. Verify Network

```bash
nodes
net
```

### 4. Test Connectivity

```bash
pingall
```

Expected result:

```text
*** Results: 0% dropped (6/6 received)
```

### 5. Check OpenFlow Rules

```bash
sudo ovs-ofctl -O OpenFlow13 dump-flows s1
```

### 6. Add Traffic Blocking Rule

```bash
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "priority=100,in_port=1,actions=drop"
```

Verify the rule:

```bash
sudo ovs-ofctl -O OpenFlow13 dump-flows s1
```

### 7. Test Packet Filtering

At the Mininet prompt:

```bash
h1 ping h2
```

Packets entering through port 1 are dropped.

### 8. Restore Network Connectivity

Remove the flow rules:

```bash
sudo ovs-ofctl -O OpenFlow13 del-flows s1
```

Test connectivity again:

```bash
h1 ping h2
pingall
```

## Cleanup

Exit Mininet:

```bash
exit
```

Clean up network configuration:

```bash
sudo mn -c
```

Stop the Ryu Controller using `Ctrl + C`.

## Result

Successfully implemented an SDN environment using Mininet, Open vSwitch, and the Ryu Controller. Verified network connectivity, inspected OpenFlow rules, implemented packet filtering, and restored network connectivity.

