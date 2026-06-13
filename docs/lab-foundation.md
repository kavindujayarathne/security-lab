# Lab Foundation

This is the starting point for the lab's foundation docs. This covers the platform the lab runs on and how the virtual machines are grouped.

## Platform

The lab runs locally on an Apple Silicon Mac, using VMware Fusion as the hypervisor. All guest VMs are ARM-based as a result.

VMware Fusion was chosen over Parallels Desktop and other options because it provides the level of low-level networking and configuration control a security lab needs. The deciding factor was the depth of network control: NAT, Host-only, custom internal networks, per-VM adapter configuration, DHCP behaviour, and direct access to the underlying configuration files (detailed in [Network Model](network-model.md)). Other hypervisors abstract these away.

The hypervisor is not just a place to run virtual machines for this lab. It is part of the lab foundation, because the network design, adapter layout, and isolation model directly shape how security scenarios are built and tested.

## Known Constraints

The ARM guest architecture is a real constraint for any work that needs native execution of x86 binaries, especially dynamic analysis of x86 PE malware samples. When this matters for a specific scenario, the workaround or limitation gets documented there, not preemptively here.

## Design Approach

The lab is organized around role-based categories rather than by every internal implementation detail.

The categories (specific machines in each are listed in [VM Inventory](vm-inventory.md)):

- Red - Offensive testing and adversary simulation.
- Blue - Monitoring and defensive visibility.
- Target - User and endpoint representation in the simulated environment.
- Sandbox - Isolated systems that must be contained from the rest of the lab.
- Infrastructure - Supporting systems for the lab itself.

This structure keeps the lab understandable while leaving room to evolve. The categories themselves and the systems inside them can both change as the lab grows.

## Related Documents

- [VM Inventory](vm-inventory.md). Each virtual machine, its category, and its role.
- [Network Model](network-model.md). Network types, per-VM adapter strategy, subnets, and addressing.
