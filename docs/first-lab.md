# First lab plan: registration, session, and local ping

Status: planned and untested. Addresses below have not been assigned. This document
is a roadmap, not instructions to perform all steps at once. Explain and obtain
approval before installations, VM creation, or system changes; wait for feedback
after each practical step.

## Environment and software

First open VirtualBox Manager in the normal Windows session and report its state.
Later verify free disk, hardware virtualization, and guest startup before provisioning.
The proposal is two Ubuntu Server 22.04 LTS guests connected to an Internal Network
named 5g-lab. Temporary NAT permits installation downloads; no bridging or port
forwarding is planned. Take snapshots after a working base installation.

Core needs Open5GS and MongoDB. Prefer official core packages. RAN needs UERANSIM,
Git, make, GCC/G++, CMake, SCTP libraries, and iproute2. UERANSIM documents CMake
at least 3.17 and GCC/G++ at least 9; verify actual package versions first rather
than blindly following old installation commands. SCTP is the transport used for
base-station control signaling. Verify Linux SCTP support and /dev/net/tun.
Linux tcpdump will capture traffic; existing Windows Wireshark will inspect files.
Subscriber administration can use the Open5GS CLI. Optional WebUI adds Node.js
and is not required for the first ping. Record exact package versions and the
UERANSIM tag/commit for reproducibility.

## Proposed addresses and matching settings

| Component | Proposed address |
| --- | --- |
| core lab interface | 192.168.56.10/24 |
| ran lab interface | 192.168.56.20/24 |
| UE address pool | 10.45.0.0/16 |
| UPF ogstun and first ping target | 10.45.0.1/16 |

Check conflicts before assigning addresses. The internal interface needs no default
gateway. Discover actual Linux interface names using ip -br addr.

Start from configuration examples for the installed versions:

- PLMN (Public Land Mobile Network identity): test MCC 001 and MNC 01. Preserve
  leading zeros and the two-digit MNC in UERANSIM.
- TAC (Tracking Area Code): 1 in gNB and AMF.
- S-NSSAI (Single Network Slice Selection Assistance Information): SST 1 without
  SD initially; match UE, gNB, AMF, and subscriber profile.
- DNN (Data Network Name): lab. UERANSIM calls the session field apn. Match core
  session configuration and subscriber permissions.
- Synthetic permanent subscriber identity (SUPI/IMSI): imsi-001010000000001 in
  UE and corresponding digits in the database. Match synthetic K and OPc keys.
  UE opType distinguishes OP from OPc; its authentication amf field is a separate
  16-bit field, not the AMF core function.
- Bind AMF N2 and UPF N3 to core's lab address, not loopback.
- gNB ngapIp and gtpIp use ran's lab address; amfConfigs points to core.
- gNB linkIp and UE gnbSearchList can use 127.0.0.1 because both run on ran.
- Verify SMF/UPF PFCP association, UE pool, DNN, and ogstun. Core-internal
  services can retain loopback communication when all run on core.

See [config documentation](../config/README.md) for future configuration filenames.

## Network paths and capture points

| Path | Transport | Capture point |
| --- | --- | --- |
| N2: gNB to AMF | SCTP 38412, NGAP signaling | core lab interface |
| N3: gNB to UPF | UDP 2152, GTP-U | core lab interface |
| N4: SMF to UPF | UDP 8805, PFCP forwarding control | core loopback for local association |
| Simulated UE to gNB radio | UDP 4997 | ran loopback |
| Inner UE packets | ICMP for first ping | core ogstun |

Permit only necessary lab traffic and ICMP from the UE pool to the target; do not
disable the firewall globally. UE needs permission to create TUN and routing;
capture tools need capture privileges.

The first target belongs to core itself, so no Internet NAT or forwarding to
another host is needed. A later separate local data-network target will require
IP forwarding, forwarding firewall rules, and a return route to the UE pool.
Any optional NAT must remain confined to the isolated lab.

## Future execution checkpoints

1. Start MongoDB and all required Open5GS 5G services; inspect status and logs.
2. Add the synthetic subscriber with matching keys, DNN, and slice.
3. Start N2/N3 capture before starting gNB.
4. Run nr-gnb with its configuration; verify successful NG Setup.
5. Run nr-ue with its configuration and TUN permissions; verify registration,
   then PDU-session establishment separately.
6. Inspect the created UE interface, IP address, routes, and policy rules.
7. Capture on ogstun and send four pings through the actual UE interface.
8. Stop and export captures to data/raw; inspect them in Windows Wireshark.
   Record versions, timestamps, scenario, interface, and run ID.

Illustrative Linux commands for a later verified configuration, not commands to run now:

```bash
# On core: replace LAB_INTERFACE with the actual interface name.
sudo tcpdump -i LAB_INTERFACE -nn -w n2-n3.pcap 'sctp port 38412 or udp port 2152'
# In another core terminal:
sudo tcpdump -i ogstun -nn -w ue-ping.pcap 'icmp'
# On ran after session establishment: verify the interface name first.
ping -I uesimtun0 -c 4 10.45.0.1
```

Wireshark display filters: ngap, gtp, and icmp. The ogstun capture exposes inner
ICMP directly. Capture files remain Git-ignored.

Acceptance criteria: successful NG Setup, UE registration, session and IP assignment,
four ping replies, inner ICMP request/reply on ogstun, and matching GTP-U on N3.
None has been verified yet. Troubleshoot in that order: IP/SCTP, subscriber keys,
DNN/slice, then TUN/routing/GTP-U/firewall. Change one thing at a time.

## Official references

- [Open5GS Quickstart](https://open5gs.org/open5gs/docs/guide/01-quickstart/)
- [Open5GS TUN example](https://open5gs.org/open5gs/docs/guide/02-building-open5gs-from-sources/)
- [UERANSIM Installation](https://github.com/aligungr/UERANSIM/wiki/Installation)
- [UERANSIM Configuration](https://github.com/aligungr/UERANSIM/wiki/Configuration)
- [UERANSIM Usage](https://github.com/aligungr/UERANSIM/wiki/Usage)
