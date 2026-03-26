# 2. HPC Part II - ⚙️ The Gearbox: Submitting a Job 
Once inside, it's time to use the actual engine.

⚠️ The Golden Rule: Never run analysis on the Login Node. The login node is the "parking lot"—it’s for editing scripts and moving files. If you start a heavy process there, you'll slow down the cluster for everyone and the sysadmin will kill your process immediately.

To access the compute power, we use the Slurm workload manager.

## 🛠️ 2.1 The "Under the Hood" Debugging (Interactive salloc) <a name="salloc-debug"></a>
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

## 📝 2.2 The "Autopilot" (Batch Job Submission) <a name="sbatch-template"></a>
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

## 🏎️ 2.3 Monitoring the Race (The Pit Wall) <a name="hpc-efficiency"></a>
Once submitted, you need to track your performance to ensure you aren't "crashing":

- Check the Queue: ```squeue -u your_username``` To see if your job is Pending or Running
- Cancel a Job: ```scancel <JOB_ID> ``` If you spot an error mid-run
- Efficiency Check: ```seff <JOB_ID>```

💡 **Biohack**: Run seff after a job finishes. If you requested 64GB but only used 4GB, you are wasting resources and slowing down the queue for everyone. Mastering your memory requests is the mark of a true HPC pro.

## 🚀 2.4 Pro-Hacks for the Finish Line <a name="hpc-pro-hacks"></a>
- The "Automatic Crew Chief" (**Email notification**): Add #SBATCH --mail-type=BEGIN,END,FAIL and #SBATCH --mail-user=your@email.com to get notified when the race starts or crashes.

- The "Clean Garage" (**Log Management**): Create a folder mkdir -p logs and use #SBATCH --output=logs/%x_%j.out to keep your workspace tidy.

- The "Starting Grid" (**Estimated Start**): Run squeue --job <JOB_ID> --start to see when Slurm thinks you will actually start.

- The "Dry Run" (**Syntax Check**): Use sbatch --test-only submit_job.sh to check for script errors without waiting in the queue.

## ⚙️ 2.5 The "Multiverse" Strategy (Job Arrays) <a name="job-arrays"></a>
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

## 🏎️ 2.6 The "Pit Stop" Dashboard (Post-Run Analytics) <a name="hpc-post-run"></a>
Once the race is over, a pro doesn't just look at the results; they look at the telemetry.

- The Efficiency Audit: ```seff <JOB_ID>``` Run this on your Array ID. If your CPU efficiency is <10%, you are requesting too many cores for a single-threaded task. If your RAM efficiency is <20%, you're "squatting" on memory that others could use.

- The History Book: ```sacct -j <JOB_ID> --format=JobName,State,Elapsed,MaxRSS```. This gives you a clean table of how much memory each subject actually used. Use this data to calibrate your next "Flight Plan.".

## ⚙️ 2.7 The "Chained Autopilot" (Job Dependencies) <a name="job-dependencies"></a>
If Job Arrays are the gearbox, *Dependencies* are your flight plan for multi-stage journeys. Stop waiting for your Mapping to finish to manually launch the Quantification.

💡 **The Hack**: Capture the Job ID of your first task and tell Slurm to start the next one only if the first one finishes successfully.

The Wrapper Script Template (run_pipeline.sh):
```Bash
#!/bin/bash
# 1. Start the Alignment Array
FIRST_GEAR=$(sbatch --parsable mapping_array.sh)
echo "Mapping started: $FIRST_GEAR"

# 2. Start the Quantification (Only after Mapping is 'ok')
SECOND_GEAR=$(sbatch --parsable --dependency=afterok:$FIRST_GEAR quant_array.sh)
echo "Quantification queued: $SECOND_GEAR"

# 3. Start the Summary Report (Even if some parts failed, using afterany)
sbatch --dependency=afterany:$SECOND_GEAR create_report.sh
```

⚠️ **The Reality Check**: While this is powerful, Slurm doesn't "know" if a specific sample failed inside the array. This "manual" orchestration is exactly why we eventually move to Nextflow.

## ⚙️ 2.8 The "Chassis" Config (Pro Folder Structure)

💡 **The Hack**: Don't let your scripts live in the same place as your Terabytes of data. One accidental ```rm -rf``` in the wrong directory could cost you months of work. Separating "code" from "storage" is what distinguishes a pro from an amateur.

In most HPC infrastructures, you have three distinct units: 

| Storage Unit | Variable | The "Hack" | Backup? |
| :--- | :--- | :--- | :--- |
| **Home** | `$HOME` | **The Blueprints:** Scripts, `.sh` files, Nextflow configs, and metadata. | ✅ YES |
| **Data** | `$DATA` | **The Warehouse:** Raw FASTQ files and consolidated final results. | ⚠️ On Request |
| **Scratch**| `$SCRATCH`| **The Workshop:** High-speed I/O for `work/` dirs and temp files. | ❌ NO |

## 🛡️ 2.9 The "Proxy" Shield (Symbolic Link Safety)

💡 **The Hack**: Never run your analysis directly inside your $DATA warehouse. If you make a typo in a cleanup script ```rm -rf *```, you could wipe out your entire project. Instead, use *Symbolic Links* (shortcuts) to create a "virtual" copy of your data in your working directory.

❓ Why this is a Pro-Move:
   - Safety: If you delete the link, the original 500GB file in $DATA remains untouched.
   - Efficiency: A symbolic link takes 0 bytes of disk space.
   - Cleanliness: You can keep your scripts in $HOME but "see" the data as if it were right there.

🛠️ How to set it up:
Instead of copying heavy files, create a link (shortcut):
```Bash
# Syntax: ln -s /path/to/real_data /path/to/shortcut
ln -s $DATA/<project_name>/raw_data ~/<project_name>/inputs
```

## ⏱️ 2.10 The "Telemetry" Audit (Computing Hours)
💡 The Hack: Do you want to know how much that analysis actually cost? Don't just trust the time you requested in the #SBATCH header; calculate the real CPU-Hour consumption by injecting this "black box" block at the end of your scripts.

**The Formula:** $$\text{CPU-Hours} = \text{Allocated CPUs} \times \text{Real Execution Time (h)}$$

Add this block to the end of your Slurm script to get the telemetry data directly in your log:
```Bash
# --- START OF TELEMETRY BLOCK ---
start_time=$(date +%s)

# >>> RUN YOUR SCIENCE HERE (Python, R, Nextflow, TrimGalore...) <<<

end_time=$(date +%s)
elapsed=$((end_time - start_time))
cpu_hours=$(echo "scale=2; ($elapsed / 3600) * $SLURM_CPUS_PER_TASK" | bc)

echo "----------------------------------------------------"
echo "🏁 RACE LOG | Project: X"
echo "CPUs: $SLURM_CPUS_PER_TASK | RAM: $SLURM_MEM_PER_NODE"
echo "Total Consumption: $cpu_hours CPU-Hours"
echo "----------------------------------------------------"
# --- END OF TELEMETRY BLOCK ---
```

## 📊 2.11 Post-Race Analytics (Historical Tracking)
If you need to report how much you’ve spent in the last month for a specific project, use the Slurm "flight recorder" command:

Total consumption for the current month:
```Bash
sacct --starttime $(date +%Y-%m-01) --format=JobID,JobName,CPUTime,Elapsed,State | grep "X"
```
