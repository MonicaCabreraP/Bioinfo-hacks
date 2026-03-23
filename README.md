# 🧬 Bioinfo-hacks
## From Raw Data to Reproducible Scientific Insights

Welcome! This repository is a living collection of the "Quality of Life" (QoL) hacks, configurations, and best practices I use to make my daily work more efficient.

As a PhD in Bioinformatics with a background in Omics and HPC, I am currently setting up the analytical pipelines for two specialized platforms (Genomics & Cytometry). This repository documents that journey: building a clean, robust, and fast environment from the ground up.

My **goal** is to share practical tips across the entire analytical stack: from navigating the HPC environment and running Nextflow pipelines to processing diverse data—including Spectral Cytometry, Single-Cell, RNA-seq, and smallRNA. Finally, I’ll show how I turn that data into high-impact visualizations to generate client reports.

---

## 📑 Roadmap of Hacks
1. [HPC & Infrastructure (Current)](#-1-hpc--infrastructure-taming-the-ferrari)
2. [Nextflow (Coming Soon)](#)
3. [smRNA processing & Reporting (Coming Soon)](#)
4. [RNA processing & Reporting (Coming Soon)](#)
5. [Single-cell with BD rhapsody processing & Reporting (Coming Soon)](#)
6. [Spectral cytometry processing & Reporting (Coming Soon)](#)

---

# 1. HPC & Infrastructure: Taming the Ferrari
Accessing an HPC cluster is only 10% of the battle. Here is the shortcut to make a remote server feel like your own computer.

💡 **The Hack**:
Stop using scp for every plot. By the end of this section, your cluster folders will appear in your sidebar just like a USB drive. You can browse UMAPs, double-click PDFs, and drag-and-drop files directly from your File Manager as if they were on your desktop.

### 🔑 1.1 The "One-Word" Login (SSH Aliases)
Stop typing long server addresses and port numbers every 10 minutes. Create a shortcut in your local ~/.ssh/config file (works on Mac, Linux, and Windows/WSL).

1. Open (or create) the file: ```vim ~/.ssh/config```
   
2. Press i to insert and paste this universal template:
     ```
    Host my_cluster (Note: Change my_cluster to whatever nickname you want, like monica).
        HostName hpc.your-institute.edu
        User your_username
        Port X
    ```
3. **Save and Exit**: Press Esc, then type :wq and hit Enter.

4. **The Result**: Instead of typing ssh -p X your_username@hpc.your-institute.edu, you just type: ``` ssh my_cluster``` ...and you are in! (You will still need your password for now).

### 🔓 1.2 Level Up: Passwordless Entry (SSH Keys)
Tired of typing your password 20 times a day? Link your workstation to the cluster securely using SSH Keys. This is not just about speed; it is the foundation for automating data transfers and pipeline triggers.

1. **Generate a secure key on your local machine:** Run ```ssh-keygen -t rsa -b 4096``` in your terminal and press Enter for all prompts.
   
2. **Copy the public key to your cluster:** Use the alias name you created in step 1.1 (e.g., my_cluster or monica):``` ssh-copy-id my_cluster```
(Note: You will have to type your password one last time here to authorize the key).

3. **The Result**: From now on, simply type ```ssh my_cluster``` ...and you are in instantly. No password prompt, no friction. 🏎️💨

### 📂 1.3 Mounting the Cluster as a Local Drive
The terminal is king for processing, but humans like folders. Let's make your HPC feel like a local drive.

   - **Mac** (Native/No Software):
   1. Open Finder.
   2. Press Cmd + K (or go to Go > Connect to Server).
   3. Type: ```sftp://my_cluster (using your alias from 1.1!).```
   4. Hit Connect. (Note: This creates a network location in your sidebar).

   - **Linux (GNOME/Ubuntu)**:
   1. Open your File Manager (Files).
   2. Click on "+ Other Locations" at the bottom of the sidebar.
   3. In the "Connect to Server" box, type: sftp://my_cluster.
   4. Hit Connect.
 
   - **Windows**: Use Cyberduck or WinSCP to mount your HPC storage via SFTP.


Maintained with ☕ by Monica @ IGTP
