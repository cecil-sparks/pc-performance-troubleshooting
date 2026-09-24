# Desktop Performance Degradation Troubleshooting Case Study

## Project Overview & Objective
This project documents a systematic, step-by-step diagnostic investigation into severe performance degradation on an entry-level desktop workstation. The workflow follows standard industry troubleshooting methodologies to isolate software inefficiencies from physical hardware bottlenecks, concluding with a precise hardware remediation plan.

---

## Phase 1: Baseline Stabilization & Patch Management
### Objective
Ensure the operating system is fully stabilized to eliminate software bugs, missing kernel patches, or legacy driver conflicts mimicking hardware failures.

### Steps Executed
1. **Initiated Windows Update:** Navigated to `Settings > Update & Security > Windows Update` and executed a manual update cycle.
2. **Patch Remediation:** Applied cumulative quality updates, feature updates, and OEM driver updates.
3. **Verification:** Reboots were finalized until the update engine confirmed the system was fully updated.

---

## Phase 2: System Architecture Baseline
### Hardware Inventory Data
* **Host Model:** HP Slim Desktop 290-p0xxx
* **Processor (CPU):** Intel Celeron G4900 @ 3.10 GHz (2 Cores, 2 Threads)
* **Physical Memory (RAM):** 4.00 GB DDR4 @ 2400 MT/s (3.81 GB usable)
* **Integrated Graphics (GPU):** Intel UHD Graphics 610 (Shared memory allocation)
* **Operating System:** Windows 11 Home (Version 23H2)

---

## Phase 3: Resource Utilization & Bottleneck Diagnosis
### Diagnostics Under Active Workload
During active operating system deployment and updates, metrics were captured via Task Manager:
* **CPU Utilization:** 21% - 53% (Normal operational thresholds)
* **Memory Utilization:** 87% - 91% (Critical saturation)
* **Disk 0 Active Time:** 100% (Absolute Bottleneck)

### Diagnostic Analysis
While physical RAM was operating near its ceiling (~90%), the primary system freezes were driven by 100% storage active time. 

Because physical RAM space was exhausted, the Windows Virtual Memory Manager was forced to engage intensive paging operations, shifting active memory segments onto the secondary storage drive's pagefile (`pagefile.sys`). A post-patch architectural audit confirmed **Disk 0 is a mechanical SATA HDD**. Because mechanical hard drives operate on low sequential read/write thresholds, the drive hit instant saturation, bottlenecking the entire system.

---

## Phase 4: Software-Level Optimization Testing
### Actions Executed
1. **Peripheral Management Assessment:** Evaluated third-party utility installation (Razer Synapse 4). Installation was **Denied** due to high background resource and telemetry overhead on a restricted 4GB memory footprint. Generic HID kernel drivers were retained.
2. **Visual Subsystem De-optimization:** Executed `systempropertiesperformance` via the native Run command (`Win + R`) and adjusted the interface for **Best Performance**. This disabled OS transparency levels and animation sets, reducing drawing overhead on shared VRAM.
3. **Startup Load Management:** Audited the Startup app hive and disabled non-critical background services (e.g., OneDrive, browser helpers) to optimize idle memory states.

### Final Verification Results (Post-Optimization Boot)
* **CPU Utilization:** 23% @ 1.68 GHz
* **Memory Utilization:** 79% (3.0 GB used)
* **Disk 0 (C:) Active Time:** 100% (Spike sustained for over 7 minutes post-boot)

---

## Final Project Verdict & Remediation Roadmap
Software optimizations have reached their absolute structural boundaries. The low raw transfer speeds inherent to mechanical SATA HDDs cannot process modern background Windows tasks quickly enough, resulting in inevitable disk pinning. 

### Recommended Engineering Upgrades
1. **Storage Subsystem Upgrade:** Procurement and deployment of a **PNY CS900 500GB 2.5" SATA III SSD**. A 2.5" SATA SSD provides a native, low-complexity drop-in replacement utilizing existing chassis data and power lanes, elevating transfer metrics from ~100 MB/s to 550 MB/s.
2. **Memory Pool Upgrade:** Expand the physical memory architecture from 4 GB to a dual-channel 8 GB or 16 GB DDR4 configuration to eliminate pagefile reliance during standard multitasking workloads.
