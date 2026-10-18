# Network configurations

No runnable YAML configuration exists yet. Start from the examples distributed
with the actual installed versions rather than assuming a schema in advance.

| Future file | Settings to adapt |
| --- | --- |
| open5gs/amf.yaml | N2 address, PLMN, tracking area, supported slice |
| open5gs/nrf.yaml | Serving PLMN as required by the installed version |
| open5gs/smf.yaml | Data network name, UE address pool, UPF/PFCP association |
| open5gs/upf.yaml | N3 address, UE subnet, ogstun |
| ueransim/open5gs-gnb.yaml | linkIp, ngapIp, gtpIp, AMF endpoint, PLMN, TAC, slice |
| ueransim/open5gs-ue.yaml | Synthetic subscriber identity and keys, gnbSearchList, session |

Core configuration normally lives under /etc/open5gs/ in the Linux guest.
UERANSIM reads the configuration passed with -c. Local configurations can go in
the Git-ignored config/local/ directory. Use only synthetic lab identities and keys.
See [the lab plan](../docs/first-lab.md) for proposed values and matching requirements.
