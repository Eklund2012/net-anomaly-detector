# Understanding the architecture

Open5GS implements the mobile core. UERANSIM simulates a UE (User Equipment,
such as a phone) and gNodeB (gNB, a 5G base station). Its radio link is simulated
over UDP; lower radio layers are not implemented. This lab cannot measure real
radio coverage, spectrum interference, or realistic radio capacity.

## Control plane and user plane

The control plane establishes and manages connections. The user plane carries
application packets, such as ping traffic.

| Component | Role |
| --- | --- |
| UE | Authenticates using a synthetic subscriber profile and sends IP packets |
| gNB | Relays signaling and tunnels UE packets to the core |
| AMF: Access and Mobility Management Function | Manages registration, access, and mobility with authentication services |
| SMF: Session Management Function | Manages data sessions and programs UPF forwarding rules |
| UPF: User Plane Function | Removes tunnel encapsulation and forwards user packets |

Additional functions include AUSF (Authentication Server Function), UDM (Unified
Data Management), UDR (Unified Data Repository), and NRF (Network Repository
Function). These support authentication, subscriber information, and service discovery.

## Packet journey

1. gNB connects to AMF over N2, the control-signaling interface.
2. UE registers and authenticates with credentials matching the lab database.
3. UE requests a PDU (Protocol Data Unit) session: a logical data connection.
   SMF manages it and programs UPF over N4.
4. UE receives an IP address and a TUN interface, a virtual interface for IP packets.
5. Ping sends ICMP (Internet Control Message Protocol) through the UE interface.
6. gNB encapsulates it in GTP-U (GPRS Tunnelling Protocol, user plane) on N3.
   Outer addresses identify gNB and UPF; inner addresses identify UE and target.
7. UPF removes encapsulation. Traffic for a separate data network continues over N6.
   Replies follow the reverse path.

AMF and SMF do not carry the ping payload. The first planned target is UPF's own
ogstun address; this demonstrates the tunnel and decapsulation, not forwarding
to a separate N6 data network.

## Captures and future Python analysis

PCAP stores captured packets and timestamps; PCAPNG is a related format.
Capture inner IP packets on core's ogstun interface for initial analysis and
capture N3 separately to understand tunneling. Do not count the two observations
of the same packet as independent samples.

Later, Scapy will read captures; Python will compute numerical features such as
packets/second, bytes/second, packet sizes, inter-arrival times, and destination
port counts. pandas will store tables in data/processed. Record capture point,
direction, scenario, and run ID so experiments can be repeated.

A future model may detect bursts, rate changes, or scanning-like patterns relative
to our baseline. These are hypotheses, not verified capabilities. Unusual traffic
is not necessarily malicious. Encrypted payloads cannot be treated as plaintext.
User-plane features do not automatically detect control-plane attacks.

Split training and test runs before generating overlapping time windows; fit any
scaling only on training data. Evaluate scenario labels and false alarms even if
using an unsupervised method such as Isolation Forest. No model exists yet.

Sources: [Open5GS architecture](https://open5gs.org/open5gs/docs/guide/01-quickstart/),
[UERANSIM Feature Set](https://github.com/aligungr/UERANSIM/wiki/Feature-Set),
[UERANSIM Usage](https://github.com/aligungr/UERANSIM/wiki/Usage).
The ML design is our project plan, not a result reported by these sources.
