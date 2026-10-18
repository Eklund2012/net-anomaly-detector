# 5g-traffic-anomaly-detection

An educational portfolio project combining 5G networks, cybersecurity, and machine learning.
The goal is to build an isolated 5G Standalone lab, capture its traffic, and later
investigate detectable traffic anomalies. Standalone uses a 5G Core without relying on 4G.

## Current status

The folder structure and lab documentation exist. No executable network configuration,
feature extractor, ML model, or automated tests have been implemented. No working 5G
network has been verified. Existing files in data/raw are sample captures, not evidence
of a completed 5G lab. No software was installed by the project setup.

## Planned architecture

```text
Ubuntu VM: ran                           Ubuntu VM: core
UE <-- simulated radio --> gNB -- N2 --> AMF
                            |            | session signaling
                            |           SMF
                            |            | N4: forwarding rules
                            +--- N3 ---> UPF -- N6 --> local data network
```

UERANSIM simulates the UE (mobile device) and gNB (5G base station).
Open5GS implements the core: AMF handles access and mobility, SMF manages data
sessions, and UPF forwards user packets. Other core functions support authentication,
subscriber data, and service discovery. No physical radio is required.
See [the architecture introduction](docs/architecture.md).

## Files and tools

| Path | Purpose and current state |
| --- | --- |
| src/extract_features.py | Empty placeholder for future Scapy packet analysis |
| tests/test_extract_features.py | Empty placeholder; no tests implemented |
| config/README.md | Describes future network configurations; no runnable YAML yet |
| data/raw/ | Local capture files, ignored by Git except .gitkeep |
| data/processed/ | Future feature tables; currently only .gitkeep |
| docs/architecture.md | Concepts, packet path, and ML limitations |
| docs/environment.md | Observed environment and remaining uncertainties |
| docs/first-lab.md | Proposed first lab and acceptance criteria |
| requirements.txt | Planned Python dependencies; does not prove installation |
| .gitignore | Excludes virtual environments, captures, local configs, and VM images |
| AGENTS.md | English language preference and educational workflow |

Planned tools: VirtualBox and Ubuntu for isolated Linux guests; Open5GS and MongoDB
for the core and subscriber database; UERANSIM for UE/gNB simulation; tcpdump and
Wireshark for packet capture and inspection. Later, Scapy reads packets, pandas
organizes features, scikit-learn provides ML algorithms, and pytest checks analysis code.

The project name is 5g-traffic-anomaly-detection. The existing working directory
remains C:\Projects\net-anomaly-detector. No Git repository metadata was found in
that directory during inspection; GitHub publication is not verified.

## Milestones

1. Completed: project skeleton, initial environment inventory, and written lab plan.
2. Pending: verify VirtualBox readiness and prepare two isolated Ubuntu VMs.
3. Pending: start the core, connect gNB, register UE, and establish a PDU session.
4. Pending: ping a local target through UE and capture the packets.
5. Future: extract and validate numerical traffic features.
6. Future: collect controlled scenarios and evaluate an anomaly model on separate runs.

## Working method and immediate next step

Proceed one practical step at a time, explain its purpose, and wait for feedback.
Ask for approval before installations or system changes. Use synthetic identities
and an isolated lab network. Python dependencies are not needed for the first 5G ping.
The existing .venv could not be verified; do not assume the generic python command
selects the intended interpreter. Pin dependency versions after a working environment
is verified.

The immediate next step is to open VirtualBox Manager in the user's normal Windows
session and report whether it opens successfully and which VMs it lists. Do not
create or modify a VM yet. See [the environment inventory](docs/environment.md)
and [the future lab plan](docs/first-lab.md).

## Official references

- [Open5GS Quickstart](https://open5gs.org/open5gs/docs/guide/01-quickstart/)
- [UERANSIM Installation](https://github.com/aligungr/UERANSIM/wiki/Installation)
- [UERANSIM Configuration](https://github.com/aligungr/UERANSIM/wiki/Configuration)
- [UERANSIM Usage](https://github.com/aligungr/UERANSIM/wiki/Usage)
- [UERANSIM Feature Set](https://github.com/aligungr/UERANSIM/wiki/Feature-Set)

References initially checked on 2026-10-18. Verify exact versions and configuration
schemas again before installation.
