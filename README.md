# 🧬 Bioinfo-hacks
## From Raw Data to Reproducible Scientific Insights

Welcome! This repository is a living collection of the "Quality of Life" (QoL) hacks, configurations, and best practices I use to make my daily work more efficient.

As a PhD in Bioinformatics with a background in Omics and HPC, I am currently setting up the analytical pipelines for two specialized platforms (Genomics & Cytometry). This repository documents that journey: building a clean, robust, and fast environment from the ground up.

My goal is to share practical tips across the entire analytical stack: from navigating the HPC environment and running Nextflow pipelines to processing diverse data—including Spectral Cytometry, Single-Cell, RNA-seq, and smallRNA. Finally, I’ll show how I turn that data into high-impact visualizations to generate client reports.
---

## 📑 Roadmap of Hacks
1. [HPC & Infrastructure (Current)](#-1-hpc--infrastructure-taming-the-ferrari)
2. [Nextflow (Coming Soon)](#)
3. [smRNA processing & Reporting (Coming Soon)](#)
4. [RNA processing & Reporting (Coming Soon)](#)
5. [Single-cell with BD rhapsody processing & Reporting (Coming Soon)](#)
6. [Spectral cytometry processing & Reporting (Coming Soon)](#)

---

## 1. HPC & Infrastructure: Taming the Ferrari
Accessing a HPC cluster is only 10% of the battle. Here is how to handle the other 90%:

### 🔑 1.1 The "One-Word" Login (SSH Aliases)
Stop typing long server addresses and your password 20 times a day. Ssh works on Mac, Linux, and Windows (PowerShell/WSL). 

1. Open (or create) the file: nano ~/.ssh/config and paste this template:

Host monica
    HostName hpc.your-institute.edu
    User your_username

2. The Result: Just type ssh monica and you are in.

### 🔓 1.2 Passwordless Entry (SSH Keys)
1. Generate a key on your laptop: ssh-keygen -t rsa -b 4096 (Press enter for all prompts).
2. Copy it to the cluster: ssh-copy-id monica. (Note: Windows users without ssh-copy-id can just manually paste their .pub key into the cluster's ~/.ssh/authorized_keys).
3. The Result: Instant login without any password prompt.

### 📂 1.3 Mounting the Cluster as a Local Drive
The terminal is king for processing, but humans like folders.

- Windows & Mac: Use Cyberduck to mount your HPC storage via SFTP.
- Linux (GNOME/Ubuntu):
  1. Open your File Manager (Files).
  2. Click on "+ Other Locations" at the bottom of the sidebar.
  3. In the "Connect to Server" box at the bottom, type: sftp://monica (using the alias we created in step 1.1!).
  4. Hit Connect.

The Hack: Your cluster folders will appear in your sidebar just like a USB drive. You can drag and drop files, browse UMAP plots, or double-click PDFs directly from the cluster as if they were on your desktop. No more manual scp for every single plot!

Maintained with ☕ by Monica @ IGTP
