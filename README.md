# 🧬 Bioinfo-hacks: From Raw Data to Reproducible Scientific Insights

Welcome! This repository is a living collection of the **"Quality of Life" (QoL) hacks**, configurations, and best practices I use to make my daily work more efficient.

During my PhD in Bioinformatics, I spent years optimizing my analytical stack to handle high-throughput Omics within HPC infrastructures. Now, I am re-deploying that expertise to build the foundational infrastructure for two specialized platforms (Genomics & Cytometry) from scratch. 

If you are new to bioinformatics, this repository is for you. My goal is to share these battle-tested tips across the entire stack: from navigating the **HPC environment** and running **Nextflow pipelines** to processing diverse data—including **Spectral Cytometry**, **Single-Cell**, **RNA-seq**, and **smallRNA**. Finally, I’ll show how I turn that data into high-impact visualizations to generate **client reports**, providing a clear starting point for your own analytical journey.

---

## 📑 Roadmap of Hacks
1. [HPC: Easy Login](#1-hpc-part-i---the-ignition-easy-login)
2. [HPC: Submitting a Job](#2-hpc-part-ii---the-gearbox-submitting-a-job)
3. [Nextflow (Coming Soon)](#)
4. [smRNA processing & Reporting (Coming Soon)](#)
5. [RNA processing & Reporting (Coming Soon)](#)
6. [Single-cell (BD Rhapsody) & Reporting (Coming Soon)](#)
7. [Spectral cytometry & Reporting (Coming Soon)](#)

---

# 1. HPC Part I - 🔥 The Ignition: Easy Login 

Accessing an HPC cluster is only 10% of the battle. Here is the shortcut to make a remote server feel like your own computer.

💡 **The Hack**: Stop using scp for every plot. By the end of this section, your cluster folders will appear in your sidebar just like a USB drive. You can browse UMAPs, double-click PDFs, and drag-and-drop files directly from your File Manager as if they were on your desktop.

### 🔑 1.1 The "One-Word" Login (SSH Aliases)
Stop typing long server addresses and port numbers every 10 minutes. Create a shortcut in your local ~/.ssh/config file (works on Mac, Linux, and Windows/WSL).

1. Open (or create) the file: ```vim ~/.ssh/config```
   
2. Press i to insert and paste this universal template:
   ```bash
    Host my_cluster 
        HostName hpc.your-institute.edu
        User your_username
        Port X
    ```
   Note: Change my_cluster to whatever nickname you want, like monica.
3. Press Esc, then type :wq and hit Enter.

4. Type: ``` ssh my_cluster``` . Instead of typing ssh -p X your_username@hpc.your-institute.edu, you just and you are in! (You will still need your password for now).

### 🔓 1.2 Level Up: Passwordless Entry (SSH Keys)
Tired of typing your password 20 times a day? Link your workstation to the cluster securely using SSH Keys. This is not just about speed; it is the foundation for automating data transfers and pipeline triggers.

1. Run ```ssh-keygen -t ed25519``` in your terminal and press Enter for all prompts to generate a secure key on your local machine.
   
2. Type ``` ssh-copy-id my_cluster``` to copy the public key to your cluster.
   *Note*: Use the alias name you created in step 1.1 (e.g., my_cluster) or monica)
           You will have to type your password one last time here to authorize the key).

4. Simply type ```ssh my_cluster``` ...and you are in instantly. No password prompt, no friction. 🏎️💨

### 📂 1.3 Mounting the Cluster as a Local Drive
The terminal is king for processing, but humans like folders. Let's make your HPC feel like a local drive.

🐧 **For Linux**
1. Open your File Manager (Files/Nautilus).
2. Click on "+ Other Locations" at the bottom of the sidebar.
3. In the "Connect to Server" box, type: ```sftp://my_cluster``` (using the alias from 1.1).
4. Hit Connect. It will appear as a mounted drive in your sidebar.

🪟 **Windows**: Use Cyberduck or WinSCP to mount your HPC storage via SFTP.

🍎 **For Mac**:

- **Option A**: Finder (Quick, but can be glitchy)
1. Open Finder.
2. Press Cmd + K (or Go > Connect to Server).
3. Type: sftp://my_cluster (using your alias from 1.1).

*Note*: This works for quick file transfers, but if you get a "File couldn't be opened" error, you need the Pro method below.

- **Option B**: The Pro Way (SSHFS) – Best for Intel/Silicon Macs
This creates a stable, permanent drive on your desktop.

⚠️ The "Security Hurdle": Modern macOS (Intel & Silicon) will block necessary extensions by default. This process requires a system restart and a one-time security tweak.

1. Install tools via Terminal:
   ```bash
   brew install --cask macfuse
   brew install gromgit/fuse/sshfs
   ```
2. Enable Kernel Extensions:
   1. Shut Down your Mac.
   2. Enter Recovery Mode: Hold the Power Button (or Cmd + R on older Intel Macs) until you see startup options.
   3. Go to Utilities > Startup Security Utility.
   4. Select your drive and click Security Policy.
   5. Check "Reduced Security" and allow "user management of kernel extensions from identified developers."

3. Restart & Approve:
   1. Restart normally.
   2. Go to System Settings > Privacy & Security and click "Allow" for the macFUSE extension.

4. Mount the drive:
   ```bash
   mkdir ~/hpc
   sshfs my_cluster:/path ~/hpc
   ```
5. Make it permanent 
   ```bash
   vim ~/.zshrc
   alias hpc='sshfs my_cluster:/path ~/hpc '
   source ~/.zshrc
   ```
6. Usage & Maintenance
   1. **To Connect**: Just type ```hpc``` in your terminal.
   2. **To Disconnect**: Type ```diskutil unmount force ~/hpc```
   (Tip: Use this if the folder looks empty or the connection "freezes" after your Mac wakes up from sleep).

### 🏆 **The Result**: Your HPC is now a native hard drive on your desktop. > Forget the terminal for a second—you can now browse cluster folders, double-click to open PDFs, and preview UMAP plots instantly in your local apps.

#Full GUI access with zero manual scp commands. 🏎️💨

# 2. HPC Part II - ⚙️ The Gearbox: Submitting a Job 
Once inside, it's time to use the actual engine.

⚠️ The Golden Rule: Never run analysis on the Login Node. The login node is the "parking lot"—it’s for editing scripts and moving files. If you start a heavy process there, you'll slow down the cluster for everyone and the sysadmin will kill your process immediately.

To access the compute power, we use the Slurm workload manager.

## 🛠️ 2.1 The "Under the Hood" Debugging (Interactive salloc)
Most users only know how to send a script and wait. But what if you need to debug a command or compile a tool in real-time?

💡 **The Hack**: Use *salloc* to reserve resources and work directly on a compute node. It’s like getting a VIP pass to the garage.

1. Request the "Lane" (Allocation):

```Bash
# Request 1 task for 2 hours
salloc -n 1 -t 02:00:00
```

2. Start the Engine (The Task):
Once granted, launch your interactive shell on that reserved node:

```Bash
srun --pty bash
```
**Pro-Tip** (The Live Look): While your salloc session is running, you can open a second terminal, SSH into that specific node (e.g., ssh node042), and run top -u your_username. This lets you see exactly how much RAM your process is burning in real-time.

## 📝 2.2 The "Autopilot" (Batch Job Submission)
For production-scale analysis, you don't want to stay connected to the terminal. You create a submission script and let the cluster handle the heavy lifting while you focus on other tasks.

1. The Template:
```Bash
#!/bin/bash
#SBATCH --job-name=my_analysis        # Name of your job
#SBATCH --output=logs/%j_res.log      # Log file (%j = Job ID)
#SBATCH --partition=short             # Partition/Queue (e.g., short, long)
#SBATCH --ntasks=1                    # Number of tasks
#SBATCH --cpus-per-task=4             # CPU cores
#SBATCH --mem=16G                     # RAM per node
#SBATCH --time=04:00:00               # Time limit (HH:MM:SS)

module load <software_name>
python my_script.py # Replace this with your actual command (Python, R, Nextflow, etc.)
```

2. Submit the job to the cluster:
```Bash
sbatch submit_job.sh
```
   ❓ **How to calculate your Resources?** The 20% Rule: If your script takes 10h, request 12:00:00. This buffer prevents "Death by Timeout."

Memory Strategy: If you don't know the RAM, start high (e.g., 32G), run a test, and then use the "Pit Wall" (Step 2.3) to downsize.

## 🏎️ 2.3 Monitoring the Race (The Pit Wall)
Once submitted, you need to track your performance to ensure you aren't "crashing":

- Check the Queue: ```squeue -u your_username``` To see if your job is Pending or Running
- Cancel a Job: ```scancel <JOB_ID> ``` If you spot an error mid-run
- Efficiency Check: ```seff <JOB_ID>```

💡 **Biohack**: Run seff after a job finishes. If you requested 64GB but only used 4GB, you are wasting resources and slowing down the queue for everyone. Mastering your memory requests is the mark of a true HPC pro.

## 🚀 2.4 Pro-Hacks for the Finish Line
- The "Automatic Crew Chief" (**Email notification**): Add #SBATCH --mail-type=BEGIN,END,FAIL and #SBATCH --mail-user=your@email.com to get notified when the race starts or crashes.

- The "Clean Garage" (**Log Management**): Create a folder mkdir -p logs and use #SBATCH --output=logs/%x_%j.out to keep your workspace tidy.

- The "Starting Grid" (**Estimated Start**): Run squeue --job <JOB_ID> --start to see when Slurm thinks you will actually start.

- The "Dry Run" (**Syntax Check**): Use sbatch --test-only submit_job.sh to check for script errors without waiting in the queue.

## ⚙️ 2.5 The "Multiverse" Strategy (Job Arrays)
If sbatch is the Autopilot, Job Arrays are the fleet management system. Instead of submitting 50 separate jobs for 50 subjects (and clogging the queue), you submit one script that spawns 50 sub-tasks.

💡 **The Power Move**: ```SLURM_ARRAY_TASK_ID```. Each "sub-job" gets a unique ID. You can use this ID to pick a specific file from a list. This is the secret to processing Patients and Controls in parallel without mixing them.

**The Template (Array Edition)**:
```Bash
#SBATCH --array=1-50%10          # Launch 50 jobs, but only 10 at a time (Concurrency)
#SBATCH --output=logs/task_%A_%a.out  # %A = Master ID, %a = Array Index

# 1. Create a simple text file with your subject IDs (e.g., subjects.txt)
# 2. Use 'sed' to grab the line corresponding to the current Task ID
SUBJECT=$(sed -n "${SLURM_ARRAY_TASK_ID}p" subjects.txt)
   
echo "Running analysis for subject: $SUBJECT"
python analysis_script.py --input data/${SUBJECT}_T0.nii.gz
```

❓ **Why this adds value** (The "Pro" Perspective): 

- *Safety First*: If you have disparate groups (like your Healthy Controls vs T0 Patients), you can run them in two separate arrays or use a mapping file to ensure the logic never crosses paths.

- *Merciless Efficiency*: If task #12 fails, you don't need to resubmit everything. You just fix the issue and run ```sbatch --array=12 submit_script.sh.```

- *Queue Karma*: Sysadmins prefer one array of 1000 tasks over 1000 individual jobs. It keeps the Slurm scheduler breathing easy.

## 🏎️ 2.6 The "Pit Stop" Dashboard (Post-Run Analytics)
Once the race is over, a pro doesn't just look at the results; they look at the telemetry.

- The Efficiency Audit: ```seff <JOB_ID>``` Run this on your Array ID. If your CPU efficiency is <10%, you are requesting too many cores for a single-threaded task. If your RAM efficiency is <20%, you're "squatting" on memory that others could use.

- The History Book: ```sacct -j <JOB_ID> --format=JobName,State,Elapsed,MaxRSS```. This gives you a clean table of how much memory each subject actually used. Use this data to calibrate your next "Flight Plan.".

.

.

Maintained with ☕ by @MonicaCabreraP



