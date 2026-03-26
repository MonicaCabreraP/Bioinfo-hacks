# 🧬 Bioinfo-hacks: From Raw Data to Reproducible Scientific Insights on HPC Environments

Welcome! This repository is a living collection of the **"Quality of Life" (QoL) hacks**, configurations, and best practices I use to make my daily work more efficient.

During my PhD in Bioinformatics, I spent years optimizing my analytical stack to handle high-throughput Omics within HPC infrastructures. Now, I am re-deploying that expertise to build the foundational infrastructure for two specialized platforms (Genomics & Cytometry) from scratch. 

If you are new to bioinformatics, this repository is for you. My goal is to share these battle-tested tips across the entire stack: from navigating the **HPC environment** and running **Nextflow pipelines**. Finally, I’ll show how I turn that data into high-impact visualizations to generate **client reports**, providing a clear starting point for your own analytical journey.

---

## 📑 Roadmap of Hacks

### 🏎️ [Phase I: Connectivity & Environment](./01-Connectivity-and-Environment/README.md#phase1)
* [1.1 One-Word Login (SSH Aliases)](./01-Connectivity-and-Environment/README.md#ssh-alias)
* [1.2 Passwordless Entry (SSH Keys)](./01-Connectivity-and-Environment/README.md#ssh-keys)
* [1.3 Mounting the Cluster](./01-Connectivity-and-Environment/README.md#ssh-mount)

### ⚙️ [Phase II: HPC Data Management](./02-HPC-Data-Management/README.md#phase2)
*Managing the engine, protecting data, and auditing resources.*

* [2.1 Interactive Debugging (salloc)](./02-HPC-Data-Management/README.md#salloc)
* [2.2 Batch Autopilot (sbatch)](./02-HPC-Data-Management/README.md#sbatch-template)
* [2.3 Monitoring the Race (The Pit Wall)](./02-HPC-Data-Management/README.md#monitoring)
* [2.4 Pro-Hacks (Email, Logs, Dry-runs)](./02-HPC-Data-Management/README.md#pro-hacks)
* [2.5 Job Arrays (Scaling Samples)](./02-HPC-Data-Management/README.md#job-arrays)
* [2.6 Post-Run Analytics (seff & sacct)](./02-HPC-Data-Management/README.md#analytics)
* [2.7 Chained Autopilot (Dependencies)](./02-HPC-Data-Management/README.md#dependencies)
* [2.8 Pro Folder Structure (The Chassis)](./02-HPC-Data-Management/README.md#storage-chassis)
* [2.9 Symbolic Link Safety (The Proxy)](./02-HPC-Data-Management/README.md#symlinks)
* [2.10 Telemetry Audit (CPU-Hours)](./02-HPC-Data-Management/README.md#telemetry)
* [2.11 Historical Tracking (Monthly Logs)](./02-HPC-Data-Management/README.md#history)
* 
### 🚀 [Phase III: Pipelines & Automation](./03-Pipelines-and-Automation/README.md)
* [Nextflow Foundations](./03-Pipelines-and-Automation/README.md) (Coming Soon)
.

.

.

Maintained with ☕ by @MonicaCabreraP



