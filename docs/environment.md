# Environment inventory - 2026-10-18

These are observations, not proof that a 5G lab works. No installations or system
changes were performed during inventory.

| Component | Observation and limitation |
| --- | --- |
| Host | Windows 11 25H2, build 26200, according to TShark |
| Hardware | Intel i5-11400F, approximately 32 GB physical RAM according to TShark; free RAM and disk not checked |
| Git | 2.45.1.windows.1; no .git directory observed in the project root |
| Python on PATH | Resolves to Cygwin, not the interpreter recorded in .venv |
| Python launcher | Present, but py --list-paths reported no installations in this execution context |
| Existing .venv | pyvenv.cfg records Python 3.13.7; execution denied access to the base interpreter; installed packages unknown |
| Docker | CLI 28.3.2 exists; config access denied and default engine pipe not found; usable engine unverified |
| WSL | Command exists; status and distribution queries returned E_ACCESSDENIED; distributions unknown |
| VirtualBox | 7.0.10r158379; list vms returned no entries in this execution context; VM startup untested |
| Wireshark/TShark | Installed under Program Files; TShark 4.6.9 reported Npcap 1.88; capture untested |
| Build tools | Cygwin gcc found; cmake not on PATH; no Linux build environment verified |
| Open5GS/UERANSIM | No installation or running Linux deployment verified |

Windows CIM queries were denied access. Hardware details came from tshark --version.
Access denial does not establish that software is absent or broken. No elevated
inventory was requested. A later host process-name check found no matching visible
VirtualBox VM or lab processes, but cannot rule out services inside WSL or other guests.

## Proposed environment

Use two Ubuntu Server VMs in existing VirtualBox: core and ran. Ubuntu 22.04 LTS
is the initial conservative proposal matching the Open5GS Quickstart example;
confirm package availability and versions before installation. Initial resource
budgets: core 2 vCPU/4 GB RAM/25 GB disk; ran 2 vCPU/2 GB RAM/20 GB disk.
These are project estimates, not official minimum requirements.

Linux provides the kernel networking environment for SCTP, TUN interfaces, routing,
and core services. Cygwin is not a replacement. UERANSIM's older installation wiki
warns against Windows/WSL; that does not prove every modern WSL2 setup is incapable.
Full VMs avoid that uncertainty. Docker is not required for the initial lab.

Use a shared VirtualBox Internal Network for lab traffic. A temporary additional
NAT adapter can provide package downloads and be disconnected for experiments.
No bridge to the home or university network is needed. Capture inside Linux and
open exported files in Windows Wireshark; Windows capture is not assumed to see
internal guest traffic.

## Immediate next step

Open VirtualBox Manager from the Windows Start menu. Report whether it opens and
which VMs it lists, or provide the error. Do not create or change a VM yet.
This checks access from the user's actual desktop session before VM planning.
Successful GUI startup does not yet prove that hardware virtualization or guest
startup works; those remain separate checks.

Sources: [Open5GS Quickstart](https://open5gs.org/open5gs/docs/guide/01-quickstart/),
[UERANSIM Installation](https://github.com/aligungr/UERANSIM/wiki/Installation),
[UERANSIM Usage](https://github.com/aligungr/UERANSIM/wiki/Usage).
