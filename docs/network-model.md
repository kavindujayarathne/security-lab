# Network Model

This document covers the network architecture of the lab. The lab uses multiple virtual network
types so systems can be connected, isolated, or exposed to controlled internet access depending on
the activity being performed.

## Network Types

The network types are listed below with their VMware Fusion labels and what a VM on each network can
reach.

| Network Type | VMware Label                | Reaches host  | Reaches internet |
| ------------ | --------------------------- | ------------- | ---------------- |
| NAT          | _Share with my Mac_         | Yes           | Yes (via NAT)    |
| Internal     | _Internal Network_ (custom) | No            | No               |
| Host-only    | _Private to my Mac_         | Yes           | No               |
| Bridged      | _Bridged Networking_        | Yes (via LAN) | Yes              |

The Internal Network is a custom network created in VMware Fusion. VMware Fusion does not ship an
internal-only segment by default. Additional custom types can be created the same way as the lab
grows.

## Per-VM Adapter Strategy

Each VM is configured with one or more network adapters depending on its role and the scenario it is
part of. Adapters can be added, removed, enabled, or disabled per VM at any time. A single VM can
take part in different topologies for different scenarios just by tweaking its adapters.

> [!NOTE]
> **Network type vs network adapter -** A network type (NAT, Internal, Host-only, Bridged) is the
> _kind of connection_. A network adapter is the VM's _virtual NIC_. Each VM can have several
> adapters, and each adapter is configured to use one network type. That's how a single VM can sit
> on multiple networks at once.

## Network Configurations

NAT, Internal, and Host-only are each implemented as a virtual network interface on the host
(`vmnet1`, `vmnet2`, and `vmnet8`) that VMware Fusion creates and manages. Subnet, DHCP, and bridge
details are configured per `vmnet` and are listed below.

> [!NOTE]
> **Host bridge vs host address -** The host bridge is the virtual interface VMware Fusion creates
> on the host for each `vmnet` (`bridge100`, `bridge101`, `bridge102`). The host address is the IP
> the host has on that network, if any. The bridge always exists; whether it carries a host IP is
> what determines if the host can talk to VMs on that network. For Internal, the bridge exists but
> has no host IP, which is why the host is excluded.

### NAT, vmnet8 (_Share with my Mac_)

| Property     | Value                              |
| ------------ | ---------------------------------- |
| Subnet       | 192.168.147.0/24                   |
| Netmask      | 255.255.255.0                      |
| NAT gateway  | 192.168.147.2                      |
| Host bridge  | bridge102                          |
| Host address | 192.168.147.1                      |
| DHCP range   | 192.168.147.128 to 192.168.147.254 |
| Upstream DNS | 1.1.1.1                            |
| DHCP         | Enabled                            |

This is the default outbound segment. Any VM that needs internet access lives here.

### Internal Network, vmnet2 (custom)

| Property     | Value                              |
| ------------ | ---------------------------------- |
| Subnet       | 192.168.110.0/24                   |
| Netmask      | 255.255.255.0                      |
| Host bridge  | bridge101                          |
| Host address | Not attached                       |
| DHCP range   | 192.168.110.128 to 192.168.110.254 |
| DHCP         | Enabled                            |

This is the segment isolated from the host, the internet, and the rest of the lab. VMs running
sandboxed work and attacker-target topologies that should not be visible outside the lab live here.

### Host-only, vmnet1 (_Private to my Mac_)

| Property     | Value                              |
| ------------ | ---------------------------------- |
| Subnet       | 192.168.218.0/24                   |
| Netmask      | 255.255.255.0                      |
| Host bridge  | bridge100                          |
| Host address | 192.168.218.1                      |
| DHCP range   | 192.168.218.128 to 192.168.218.254 |
| DHCP         | Enabled                            |

This is the private segment shared between the host and selected VMs. VMs that need to communicate
with the host but stay off the internet live here.

### Bridged Networking

Bridged Networking has no `vmnet` of its own. The VM sits directly on the host's LAN as an
independent peer, with its own IP leased from the LAN's DHCP (not from VMware Fusion). Traffic
passes through the host's physical interface (`en0`, etc.) because that's the way out, but the VM is
a regular device on the network. It isn't translated or addressed by VMware Fusion the way NAT VMs
are.

VMs that need to appear as a device on the host's physical LAN live here.

## Static IP Assignments

Static IPs are pinned at the hypervisor level by mapping a guest MAC address to a fixed IP inside
the relevant `dhcpd.conf`. This keeps addressing consistent across VM reboots without needing any
guest-side network configuration.

A small number of hosts are already pinned on the NAT segment. Everything else uses DHCP-leased
addresses. New static assignments are made as scenarios call for them, since most VM roles in the
lab are defined by what scenarios need rather than chosen upfront.

## Configuration Location

VMware Fusion stores its network configuration under `/Library/Preferences/VMware Fusion/` on the
host. The files that matter are:

- `networking`: top-level VMware Fusion network configuration. Lists `vmnet` interfaces, their DHCP
  and host-adapter settings, subnets, and display names.
- `vmnet1/`, `vmnet2/`, `vmnet8/`: per-network configuration directories.

These files are owned by `root`. The VMware Fusion app handles surface-level configuration.
Lower-level networking configurations are managed by editing these files directly.

This location is the single source of truth for the lab's network state. The configuration files
themselves are not copied into the repository.
