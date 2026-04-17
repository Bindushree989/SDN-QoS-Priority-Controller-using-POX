# SDN QoS Priority Controller using POX

## Problem Statement
The objective of this project is to implement a Software Defined Networking (SDN) solution using Mininet and a POX controller to demonstrate:
- Controller–switch interaction
- Flow rule design using OpenFlow (match–action)
- Network behavior observation
- Basic QoS (Quality of Service) implementation

---

## Project Overview
This project implements a QoS-enabled learning switch using the POX controller. The controller dynamically:
- Learns MAC addresses
- Handles PacketIn events
- Installs flow rules in the switch
- Classifies traffic based on protocol
- Assigns priority to different types of traffic

---

## Technologies Used
- Mininet (Network Emulator)
- POX Controller (SDN Controller)
- OpenFlow 1.0 Protocol
- Open vSwitch (OVS)
- Python
- iperf (Throughput testing)
- ping (Latency observation)


---

## Setup & Execution Steps

### 1. Start Mininet
```bash
sudo mn --topo single,3 --controller=remote
```

### 2. Run POX Controller
```bash
cd ~/pox
./pox.py openflow.of_01  misc.your_controller_file_name
```

### 3. Verify Connectivity
```bash
pingall
```

Expected Output:
```
0% packet loss
```

---

## Controller Logic
- MAC Learning: Stores MAC-to-port mapping
- Packet Handling: Uses PacketIn events
- Flow Rules: Installed using OpenFlow (match → action)
- QoS Classification:

| Protocol | Type           | Priority   |
|----------|----------------|------------|
| TCP      | HTTP / iperf   | HIGH (100) |
| ICMP     | ping           | LOW (10)   |
| Others   | UDP etc        | MEDIUM (5) |
| Non-IP   | ARP            | LOWEST (1) |

---

## Testing & Validation

### Test Case 1: Connectivity
```bash
pingall
```
Expected: All hosts reachable with 0% packet loss

---

### Test Case 2: ICMP Traffic (Low Priority)
```bash
h1 ping h2
```
Expected:
- Successful ping
- Controller log shows ICMP → LOW PRIORITY

### Test Case 3: Throughput Test (iperf)
```bash
iperf h1 h2 

- This command generates TCP traffic between hosts.
- Mininet automatically runs iperf server on h2 and client on h1.
- Used to measure throughput and observe QoS behavior.

Expected:
- TCP traffic is generated
- Controller logs show TCP → HIGH PRIORITY
- Flow table contains entries with protocol = 6 and high priority
```

### Test Case 4: Flow Table Verification
```bash
dpctl dump-flows
```

Expected:
- TCP flows → priority 100
- ICMP flows → priority 10

---

## Latency Measurement

### ICMP Latency
ICMP latency is measured using the `ping` command, which provides Round Trip Time (RTT).

```bash
h1 ping h2
```

- The `time` field in the output represents latency (in milliseconds).
- The first packet typically shows higher delay due to controller involvement.
- Subsequent packets show lower latency due to installed flow rules.

---

### TCP Latency
TCP latency is measured indirectly using application-level response time.

```bash
time h1 wget http://10.0.0.2
```

or

```bash
h1 curl -o /dev/null -s -w "Time: %{time_total}\n" http://10.0.0.2
```

- The total execution time represents TCP response delay.
- Lower response time indicates better performance.
- TCP traffic benefits from higher priority in the controller.

---

## Performance Observation & Analysis

| Metric             | Observation                                        |
|--------------------|----------------------------------------------------|
| Latency (ICMP)     | First packet higher delay, later packets faster    |
| Latency (TCP)      | Measured via response time (wget/curl)             |
| Throughput (iperf) | Stable TCP performance                             |
| Flow Table         | Dynamically updated                                |
| QoS Behavior       | TCP traffic prioritized over ICMP                  |

---

## Proof of Execution

All screenshots and output logs are included in the repository under the folder:

/output_screenshots

This folder contains:
- pingall output
- ICMP ping results
- iperf results
- flow table (dpctl dump-flows)
- controller logs

---

## Key Observations
- First packet experiences delay due to controller processing
- Subsequent packets are faster due to installed flow rules
- TCP traffic is given higher priority compared to ICMP
- SDN enables centralized and dynamic network control

---

## Conclusion
This project demonstrates:
- SDN architecture using POX and Mininet
- Dynamic flow rule installation using OpenFlow
- QoS implementation using priority-based handling
- Improved performance using flow-based forwarding

---

## References
- POX Controller Documentation
- Mininet Documentation
- OpenFlow Switch Specification
- SDN course materials

---

