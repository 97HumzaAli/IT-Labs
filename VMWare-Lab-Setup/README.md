# VMware + Gaming Workstation Build (2025) — Part 1: Hardware Overview

## Project Summary
This stage focuses on assembling and documenting a hybrid workstation designed for future virtualization and development workloads.  
The system is built to support VMware ESXi 8, pfSense, and high-performance desktop use such as gaming or software testing.  
The objective is to create a workstation that feels responsive as a daily driver but offers the reliability and expandability of an enterprise server.  
This part covers only the hardware selection, assembly, and reasoning behind each component.

---

## Skills Practiced
- Hardware research and compatibility planning  
- Selecting components optimized for virtualization  
- Understanding server-grade features such as ECC, VT-d, and SR-IOV  
- Designing airflow and thermal layouts  
- Planning for integration with physical enterprise switches  

---

## Hardware Configuration

| Component | Model |
|------------|--------|
| **CPU** | Intel Core i7-14700K (20 C / 28 T, Raptor Lake Refresh) |
| **Motherboard** | ASUS Pro WS W680-ACE (DDR5) |
| **Memory** | Kingston FURY Renegade 64 GB (2 × 32 GB) DDR5-6000 CL30 |
| **Boot Drive** | WD Black SN770 500 GB PCIe 4.0 NVMe |
| **Main Datastore** | WD Black SN850X 2 TB PCIe 4.0 NVMe |
| **Network Card** | Intel X550-T2 Dual 10 Gb Ethernet (RJ-45) |
| **GPU** | NVIDIA RTX 3060 Ti |
| **CPU Cooler** | Noctua NH-U12S (with LGA 1700 kit) |
| **Case** | Jonsbo TK3 Separated Cabinet Case |
| **Power Supply** | Corsair CX750M 750 W 80+ Bronze |

---

## System Rationale

### 1. Performance for Virtualization
The Intel Core i7-14700K provides 8 Performance cores and 12 Efficiency cores (20 total cores, 28 threads).  
The P-cores handle demanding workloads while the E-cores manage background or virtual-machine tasks.  
VMware ESXi 8 recognizes Intel’s hybrid architecture, allowing efficient VM scheduling across both core types.  
This CPU provides enough processing power to run 10–15 virtual machines while maintaining desktop responsiveness.

---

### 2. Workstation-Class Foundation
The ASUS Pro WS W680-ACE is a workstation-grade motherboard built for reliability.  
It includes several enterprise-level technologies:  

- **ECC Memory (Error-Correcting Code):** Detects and corrects single-bit memory errors automatically, preventing data corruption inside VMs.  
- **VT-d (Intel Virtualization Technology for Directed I/O):** Allows direct assignment of physical hardware such as GPUs or NICs to virtual machines for higher performance and isolation.  
- **SR-IOV (Single Root I/O Virtualization):** Enables one physical network adapter to appear as multiple virtual NICs that can be shared across several VMs while maintaining hardware-level throughput.  

The board’s dual Intel 2.5 Gb LAN ports can serve management, storage, or test networks, offering flexibility for enterprise-style lab setups.

---

### 3. Storage Layout
Two PCIe 4.0 NVMe SSDs separate system and data operations.  
The WD Black SN770 (500 GB) will be used for the ESXi boot image and log files, while the WD Black SN850X (2 TB) will hold VM datastores and a Windows partition.  
Both drives use **TLC NAND (Triple-Level Cell)** flash, balancing speed, endurance, and cost—ideal for sustained I/O workloads.

---

### 4. Networking and Physical Integration
The Intel X550-T2 dual-port 10 Gb adapter has native driver support in ESXi 8.  
It allows dedicated networks such as:  
- Management Network for ESXi access  
- vMotion Network for live migration  
- Storage Network for iSCSI or NAS traffic  
- pfSense VM Network for routing and firewall operations  

The card also integrates with physical enterprise switches (Cisco Catalyst, HP Aruba, UniFi aggregation models).  
Using VLAN tagging (Virtual LANs) and trunk ports, virtual networks can extend into physical infrastructure for Layer 2 and Layer 3 experiments.

---

### 5. Memory Bandwidth
64 GB of DDR5-6000 CL30 provides high bandwidth and low latency.  
**CAS Latency (CL)** measures how many clock cycles it takes for memory to respond to a request—lower values like CL30 mean faster access.  
This configuration improves responsiveness under multiple simultaneous workloads.  
The motherboard supports upgrades up to 192 GB for future expansion.

---

### 6. Thermals and Acoustics
The Noctua NH-U12S offers efficient and quiet cooling for the 14700K.  
The Jonsbo TK3 uses a dual-chamber design that isolates the PSU and drives from the main airflow path.  
Airflow is optimized with three front intakes, three top exhausts, and one rear exhaust, keeping temperatures stable under load.

---

### 7. Expandability
The platform supports multiple PCIe devices and up to 192 GB of memory.  
With 10 Gb networking and PCIe 4.0 storage, the system can later add more NICs, NVMe drives, or NAS integration without changing core hardware.

---

## Scenario
This stage represents the physical foundation for an enterprise-style virtual lab.  
No virtualization software has been installed yet.  
The hardware has been assembled, tested for boot stability, and verified for future VMware ESXi deployment.  
Upcoming labs will use this system to host pfSense, Active Directory, and Windows Server environments with realistic network segmentation.

---

## Conclusion
This build establishes a balanced platform that combines desktop usability with enterprise-grade capability.  
Features like ECC Memory, VT-d, and SR-IOV make it ideal for hands-on virtualization and infrastructure testing.  
All components were chosen for ESXi 8 compatibility and long-term reliability.  
The system is now fully assembled and ready for Part 2, where VMware ESXi will be installed as the base hypervisor.

---

## Glossary of Terms
**ECC Memory:** Detects and corrects minor memory errors automatically to prevent data corruption.  
**VT-d:** Intel technology that lets a VM access physical hardware directly for better performance.  
**SR-IOV:** Allows a single physical network adapter to create multiple virtual interfaces for use by different VMs.  
**VLAN (Virtual LAN):** A logical segmentation of a physical network to control traffic and isolate devices.  
**TLC NAND:** Flash storage that stores three bits per cell, balancing speed, endurance, and capacity.  
**CAS Latency (CL):** The number of clock cycles needed for RAM to respond to a command; lower is faster.  

---
