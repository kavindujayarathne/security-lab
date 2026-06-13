# VM Inventory

This document lists each VM in the lab grouped by category. The categories themselves are described in [Lab Foundation](lab-foundation.md). Both the categories and the VMs that belong to them may change as the lab grows.

## Red

Offensive systems used to simulate attackers against the rest of the lab.

| Name      | Operating System | Role                  |
|-----------|------------------|-----------------------|
| kali-main | Kali Linux       | Attacker workstation. |


## Blue

Defensive systems responsible for visibility and monitoring inside the lab.

| Name | Operating System | Role        |
|------|------------------|-------------|
| SOC  | Ubuntu Server    | Wazuh SIEM. |


## Target

Endpoints that live inside the simulated environment as systems to be attacked, observed, and protected.

| Name         | Operating System | Role                  |
|--------------|------------------|-----------------------|
| windows-user | Windows          | End-user workstation. |


## Sandbox

Isolated systems used for research, testing, and disposable work that should not touch the rest of the lab.

| Name            | Operating System | Role         |
|-----------------|------------------|--------------|
| windows-malware | Windows          | Malware lab. |

Sandbox systems default to the Internal Network only, with no other adapters connected.

## Infrastructure

Supporting systems with general-purpose or undecided roles.

| Name            | Operating System  | Role                               |
|-----------------|-------------------|------------------------------------|
| ubuntu-server   | Ubuntu Server     | Internal services host.            |
| ubuntu-operator | Ubuntu Desktop    | General-purpose Linux workstation. |
| ubuntu-temp     | Ubuntu (live ISO) | Live ISO VM for quick checks.      |


## Notes

The inventory is expected to change as the lab grows. Roles may be refined over time as new monitoring, detection, malware analysis, and attack simulation scenarios are introduced.
