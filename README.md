# Cable attached to two network namespaces
Link two network namespaces (direct cable, no outside)

# Link two network namespaces (direct cable, no outside)
## purchasing and hr namespaces

Add a network namespace named purchasing
```
ip netns add purchasing
```

Add a network namespace for hr
```
ip netns add hr
```

List network namespaces
```
ip netns
```

On the linux host you can view the network interaces with
```
ip link
```

View interfaces on purchasing namespace
```
ip netns exec purchasing ip link
```
```
ip -n purchasing link
```

View arp table on the linux host
```
arp
```

View ARP table in purchasing namespace
```
ip netns exec purchasing arp
```

View ROUTE table on linux host
```
route
```

View ROUTE table in purchasing namespace
```
ip netns exec purchasing route
```
# L2 cabling
# Add virtual cable between two namespaces

Create the cable that will connect purchasing and hr
```
ip link add eth0-purchasing type veth peer name eth0-hr
```

Attach each side of the cable to each namespace:
```
ip link set eth0-purchasing netns purchasing
```
```
ip link set eth0-hr netns hr
```


# L3 config
# Add IP addresses on each virtual interface
```
ip -n purchasing address add 192.168.1.1 dev eth0-purchasing
```
```
ip -n hr address add 192.168.1.2 dev eth0-hr
```

## Unshut the interfaces

Bring link UP for purchasing
```
ip -n purchasing link set eth0-purchasing up
```
Bring link UP for hr
```
ip -n hr link set eth0-hr up
```

# Test connectivity between the two namespaces
## From purchasing:
ping hr
```
ip netns exec purchasing ping 192.168.1.2
```
Check ARP table
```
ip netns exec purchasing arp
```

## From hr
ping purchasing
```
ip netns exec hr ping 192.168.1.1
```
Check ARP table
```
ip netns exec hr arp
```
