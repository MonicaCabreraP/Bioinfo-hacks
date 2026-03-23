# Bioinfo-hacks
# 🧬 : From Raw Data to Scientific Insights

Welcome! This repository is a living collection of **Quality of Life (QoL) hacks**, configurations, and best practices for Bioinformatics.
My goal is to bridge the gap between high-performance computing power and biological discovery, with a special focus on **Single-Cell RNA-seq** and **Spectral Cytometry**.

---

## 📑 Roadmap of Hacks
1. [🏎️ HPC & Infrastructure (Current)](#-1-hpc--infrastructure-taming-the-ferrari)
2. [🧪 Single-Cell RNA-seq (Coming Soon)](#-2-single-cell-rna-seq-tricks)
3. [🌈 Spectral Cytometry (Coming Soon)](#-3-spectral-cytometry-workflows)

---

## 🏎️ 1. HPC & Infrastructure: Taming the Ferrari
Accessing a cluster is only 10% of the battle. Here is how to handle the other 90%.

### 🔑 1.1 The "One-Word" Login (SSH Aliases)
Stop typing long server addresses and your password 20 times a day. Create a shortcut in your local `~/.ssh/config` file:

1. Open (or create) the file: `nano ~/.ssh/config`
2. Paste this template:
   ```text
   Host monica
       HostName hpc.your-institute.edu
       User your_username
   ```
  3. The Result: Just type ssh monica and you are in.

🔓 1.2 Passwordless Entry (SSH Keys)
Generate a key on your laptop: ssh-keygen -t rsa -b 4096 (Press enter for all prompts).

Copy it to the cluster: ssh-copy-id monica.

The Result: Instant login without any password prompt.

📂 1.3 Mounting the Cluster as a Local Drive
The terminal is king for processing, but humans like folders. Use Cyberduck to mount your HPC storage via SFTP.

The Hack: Browse UMAP plots or double-click PDFs directly from the cluster as if they were on your desktop. No more manual scp for every plot!


Maintained with ☕ by Monica
