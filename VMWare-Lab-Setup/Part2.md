# VMware + Gaming Workstation Build (2025) — Part 2: BIOS/UEFI Setup for ESXi

## Project Summary
This stage documents the **BIOS/UEFI baseline** I use on the **ASUS Pro WS W680-ACE IPMI** to run **VMware ESXi 8.0 U3**.

> **Board/BIOS:** ASUS Pro WS W680-ACE IPMI — BIOS Version 4101  
> **CPU/RAM:** Intel i7-14700K with 64 GB DDR5-6000  
> **NIC:** Intel X550-T2 (10 GbE)  
> **GPU:** NVIDIA RTX 3060 Ti

---

## Skills Practiced
- Configure BIOS for **virtualization** (VT-x, VT-d, SR-IOV, IOMMU)
- Configure BIOS for **stable PCIe** behavior (4G Decoding, ASPM, VMD)
- **UEFI-only** boot path and Secure Boot decisions for ESXi
- Fan curves and **remote management (IPMI)**

---

## BIOS Configuration

### Core Virtualization
| Setting | Value | Why |
|---|---|---|
| **Intel VT-x (Virtualization Tech)** | **Enabled** | Required to run VMs and nested hypervisors. |
| **Intel VT-d (IOMMU)** | **Enabled** | Enables GPU/NIC/USB passthrough with isolation. |
| **IOMMU Pre-Boot Behavior** | **Enable IOMMU** | Maps devices early to avoid instability during initialization. |
| **SR-IOV Support** | **Enabled** | Lets the X550-T2 create virtual NICs so each VM gets its own fast lane.|
| **Hyper-Threading** | **Enabled** | Gives ESXi all logical threads for better scheduling of VMs |

### System & PCIe
| Setting | Value | Why |
|---|---|---|
| **Above 4G Decoding** | **Enabled** | Required for large PCIe device memory(BAR) devices; avoids address space issues. |
| **Re-Size BAR Support** | **Enabled** | Optimizes GPU/PCIe memory mapping. |
| **PCIe ASPM / L1 Substates** | **Disabled** | Prevents link power-state flapping on NICs/GPUs by utilizing standard power instead of power saving features. |
| **VMD Controller** | **Disabled** | Presents NVMe drives directly to ESXi (avoids controller layer such as Intel VMD/RAID). |

### Boot & Power
| Setting | Value | Why |
|---|---|---|
| **CSM (Compatibility Support Module)** | **Disabled** | Forces pure UEFI which is required for ESXi 8 installs. |
| **Secure Boot** | **Other OS (Disabled)** | Avoids signature/driver friction during installs and passthrough. |
| **Boot Mode** | **UEFI Only** | Clean NVMe boot path and predictable device ordering. |
| **Restore AC Power Loss** | **Power On** | Auto-recovery after outages—useful for lab uptime. |

### 🌡️ Cooling & Monitoring
| Setting | Value | Why |
|---|---|---|
| **Q-Fan Control** | **PWM Mode** | Granular fan curve control. |
| **Fan Profiles** | **Silent / Standard** | Quiet baseline; ramps when needed. |
| **IPMI (BMC)** | **Enabled** | Remote KVM, power control, sensors—hands-off management. |

---

## System Rationale
1. **Virtualization First:** VT-x + VT-d + SR-IOV deliver clean passthrough and virtual functions on the X550-T2 for segmented ESXi networks (mgmt/vMotion/storage/lab VLANs).  
2. **Stable PCIe Map:** Above 4G Decoding + Re-Size BAR prevent BAR space conflicts on modern GPUs/NICs. If a VM’s GPU passthrough misbehaves, I toggle Re-Size BAR **off** first.  
3. **NVMe Efficiency:** Disabling **VMD** exposes NVMe directly to ESXi.  
4. **UEFI-Only Path:** CSM off + UEFI only eliminates legacy boot incompatibility.  
5. **Secure Boot Choice:** I run **Secure Boot disabled** here for maximum driver compatibility; I’ll re-enable later if stable.  
6. **Hands-Off Ops:** IPMI means I can power-cycle, mount ISOs, and watch POST remotely. Combined with sensible fan curves, this runs 24/7.

---
