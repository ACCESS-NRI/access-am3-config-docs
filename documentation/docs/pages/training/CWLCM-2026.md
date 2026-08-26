---
marp: true
theme: default
paginate: true
backgroundColor: #fff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
style: |
  table {
    width: 100%;
    margin: 0 auto;
    margin-top: 1em;
    font-size: 0.75em;
  }
  h1 {
    font-size: 1.5em;
  }
  h2 {
    font-size: 1em;
  }
  p {
    font-size: 1em;
  }
  img {
    width: 50%;
    display: block;
    margin-left: auto;
    margin-right: auto;
  }
  .note {
    background: #e7f3fe;
    border-left: 6px solid #2196F3;
    padding: 12px 16px;
    border-radius: 4px;
    margin: 16px 0;
  }
---

# ACCESS-AM3 Training

**ACCESS Community Workshop on Land and Coupled Modelling, 2026**

*Dr. Benjamin J. E. Schroeter (ACCESS-NRI)*

---

The following is a guided walkthrough of the [ACCESS-AM3 Run a Model](https://docs.access-hive.org.au/models/run_a_model/run_access-am3/) docs for the purposes of the Coupled/Land Workshop 2026 training.

---

## Target Audience

This training is designed to cater to a range of skill levels, from those just starting out to those who have been working with numerical models throughout their career. However, in order to appeal to the broadest audience, the instructions that follow start from the basics.

**Assumptions**
- You've completed the prerequisites
- You are familiar with Git/Github (and you have it set up on Gadi)
- You are comfortable using the Linux command line
- You have experience using the Australian Research Environment (ARE) at NCI

---

## Required technical setup

Before you can begin the training, the following technical setup steps are required:

1. An NCI Account
2. [Access to the ACCESS-AM3 configurations repository](https://forum.access-hive.org.au/t/request-access-to-am3-configurations/5580)
3. Membership to the following projects on NCI
    - [access](https://my.nci.org.au/mancini/project/access/join): ACCESS software sharing
    - [vk83](https://my.nci.org.au/mancini/project/vk83/join): ACCESS Models 
    - [xp65](https://my.nci.org.au/mancini/project/xp65/join): ACCESS Analysis Environments
    - [hr22](https://my.nci.org.au/mancini/project/hr22/join): Cylc Rose Workflow 
    - [nf33](https://my.nci.org.au/mancini/project/nf33/join): Training compute resources

If you have not completed these steps, you may not be able to follow the training and will need to pair up with someone who has.

---

## Start a Gadi Terminal Session on ARE

While it is possible to complete this training over an SSH connection from your local machine (and you are welcome to), we will be using the Australian Research Environment's (ARE) Gadi Terminal session to ensure a consistent training experience.

To start a Gadi Terminal session:
1. Go to the [Australian Research Environment](https://are.nci.org.au/) website and login with your <br/>**NCI username and password**.
2. Click on Gadi Terminal under "All Apps" (search for it if not visible)

---

## Set up the Cylc 8 workflow environment

The Cylc 8 workflow environment contains all of the tools necessary to configure and run numerical workflows.

Within the terminal, enter the following commands to load the Cylc 8 module:   

  ```shell
  module use /g/data/hr22/modulefiles
  module load cylc/8.6.3
  ```

You will now have access to the Cylc workflow engine and model execution infrastructure.

---

## Get the ACCESS-AM3 release configuration

The ACCESS-AM3 release configuration is maintained on GitHub. To access the released configuration, execute the following commands within your Gadi terminal.

```shell
# Create the roses directory and move into it
mkdir -p ~/roses && cd ~/roses

# Clone the configurations repository
git clone git@github.com:ACCESS-NRI/access-am3-configs.git

# or, if you prefer HTTPS (and have it set up correctly)
git clone https://github.com/ACCESS-NRI/access-am3-configs.git

# Move into the configs directory, check out the release config
cd access-am3-configs && git checkout release-n96e-3.0
```

You now have the released ACCESS-AM3 N96 configuration.

---

## Detached what?!

Checking out the configuration with the previous commands will report that the repository is now in a "Detached HEAD" state. This is expected and simply indicates that you have checked out a specific commit, not any particular branch.

---

## Exercise: Adjust the run length and project

The default configuration has a run length of 12 months and inherits the default user project. For the purposes of this training, we will adjust the run length to something shorter so that we can see the workflows to completion and change the project to `nf33`.

You can make these adjustments by editing the `rose-suite.conf` file using the editor of your choice (i.e. `vim`, `nano`):

```shell
STORAGE_PROJECT='nf33'
COMPUTE_PROJECT='nf33'
...
EXPT_RUNLEN='P1M'
```

You have now adjusted the run length of the default ACCESS-AM3 configuration to 1 month and set it to use the training project resources.

---

## Start a persistent session

The Cylc workflow engine runs on the NCI persistent sessions service. In order for the workflow to run, a persistent session must be available.

Run the following commands in the terminal:

```shell
# Ensure that your persistent session setup is configured correctly
/g/data/hr22/bin/gadi-cylc-setup-ps -y
```

The output of this script is verbose, but should conclude with some variation of<br/>`RESULT: PASSED`.

---

## Start a persistent session

You may now start a persistent session with the following command:

```
persistent-sessions start -p nf33 cylc
```

Your persistent session will start with the name `cylc.$USER.nf33.ps.gadi.nci.org.au`.

---

## Assign the persistent session to Cylc

In order to assign this session to Cylc, it needs to be added to a file located at `~/.persistent-sessions/cylc-session`. If you have used persistent sessions with other models, it may also be useful to assign this to the `CYLC_SESSION` environment variable. Do both with the following commands.

```shell
export CYLC_SESSION=cylc.$USER.nf33.ps.gadi.nci.org.au
echo $CYLC_SESSION > ~/.persistent-sessions/cylc-session
```

Now that your persistent session is assigned to Cylc, you can run ACCESS-AM3.

---

## Start the workflow

Cylc 8 has 3 steps to do this from the configuration directory:

- `cylc validate .` - Validates the workflow configuration.
- `cylc install .` - Installs the workflow to the working directory.
- `cylc play .` - Runs the workflow.

The Cylc developers have provided a shorthand that will execute these in sequence to save time, `cylc vip`. To run the workflow under the training project, execute the following command:

```shell
PROJECT=nf33 cylc vip
```

The workflow will now execute and start submitting tasks to the scheduler (PBS).

---

## But what if it didn't start?

If Cylc complains that it is unable to connect to a persistent session, this is symptomatic of a conflict in your persistent sessions configuration and usually due to project membership or a failure to exchange SSH keys.

Try logging into your persistent session to initiate the exchange, logging out, then trying again:
1. `ssh cylc.$USER.nf33.ps.gadi.nci.org.au`
2. `logout`
3. `cylc vip`

---

## Monitor the workflow

Now that the workflow is running, we have the ability to monitor its progress. There are multiple options to do this, today we will make use of the Terminal User Interface (TUI).

Within the terminal, execute the following command:

```shell
cylc tui
```

From this interface you can monitor workflow progress, check logs and control exection of tasks. Take some time to navigate the TUI to understand how it works.

---

## Exercise: Logs

The tasks `atmos_main`, `install_ancil` etc. will change state as they move through their execution, see if you can open and view the logs as they become available.

Bonus points if you can figure out how to open the log in `vim` *through* the TUI. This will enable you to access additional editor functionality to search through the logs.

---

## Check the workflow has completed

At the completion of the workflow, the TUI will simply say that the workflow has the state `stopped`. This can be ambigious as (depending on the nature of the error) it can also mean that the workflow has failed. The TUI also unhelpfully hides completed tasks by default. This can be adjusted with task filters if the workflow is still on the scheduler.

A sure-fire way to check if the workflow has completed if you're unsure is to look at the logs, in particular the overall scheduler log.

<div class="note">For brevity, we will use `$WORKFLOW_DIR` in place of `$HOME/cylc-run/access-am3-configs` for the following examples.</div>

---

Exit the TUI with `q` and try the following commands:

```shell
# For brevity
export WORKFLOW_DIR=$HOME/cylc-run/access-am3-configs

# Move to the cylc-run directory
cd $WORKFLOW_DIR

# Check the scheduler log for failures
cat runN/log/scheduler/log | grep "fail"

# Check the scheduler log for successful tasks
cat runN/log/scheduler/log | grep "submitted to"
cat runN/log/scheduler/log | grep "succeeded"
```

If you get no failures, and as many "succeeded" lines as there were tasks submitted, the workflow has completed successfully.

---

## Log Files - Finding and Interpreting Them

While on the subject of logs, ACCESS-AM3 produces the following categories of logging information:
- Cylc Logs
- UM Logs
- PBS Logs

---

## Cylc Logs

The Cylc task logs exposed by the TUI are written by default to `$WORKFLOW_DIR/run[N]/log`.

There are a number of files and folders within this directory, however, the most useful items are:

- `rose-suite-run.log`: the output you would have seen upon starting the workflow
- `job`: a directory containing the jobs (tasks) within the workflow.

---

The `job` directory contents follow a certain structure:

`job/[TIMESTAMP]/[TASK_NAME]/[RUN_ATTEMPT]`

Where:
- `TIMESTAMP` is the cycle point
- `TASK_NAME` is the name of the app (component) of the workflow
- `RUN_ATTEMPT` is the run attempt of the app, with NN symlinked to the latest run

---

Within the latest run, the following files may be present:
- `job`: The compiled script used to launch the job
- `job-activity.log`: Event history of the job on the scheduler
- `job.err`: Captured error messages output to STDERR
- `job.out`: Captured messages output to STDOUT
- `job.status`: The current status of the job

These files are the first place to look when diagnosing model issues.

---

## UM Logs

Further, you may also look at the raw UM log files (sometimes referred to as "pe_output" or similar) for the last few cycles, available at:

`$WORKFLOW_DIR/runN/work/[TIMESTAMP]/atmos_main/pe_output`

These logs contain timestep-by-timestep output and is where most model-level errors appear (e.g. instabilities, failed reads of ancillary files) and may help to diagnose your issue.

---

## PBS Logs

The PBS job ID is captured when a task is submitted. `qstat -f <jobid>` can be used to retrieve PBS-level information about walltime, memory, and exit codes. This can be useful if a job hasn't started (i.e. your project is out of resources for instance).

For a given task, the PBS log (sometimes referred to as "PBS out") is located in the `job.out` file as described earlier.

---

## Model output

Model output is written to the following path:

`$WORKFLOW_DIR/runN/share/data/History_Data`

Within this directory are the raw model output files, which follow the naming convention `*.p[a-m]YYYYMMM`, each representing a different output stream or dump frequency.

---

For convenience, the most frequently used data have been converted to NetCDF under:

`$WORKFLOW_DIR/runN/share/data/History_Data/netCDF`

These output files can be opened in the software of your choice to visualise and interpret results.

---

## Ancillary Files

Ancillary files (ancillaries) are pre-processed input fields used by the UM for quantities that aren't computed interactively - SSTs, sea ice, ozone, aerosol emissions, land use, soil properties, etc.

The locations of the ancillary files used by ACCESS-AM3 are detailed in the following configuration file:

`~/roses/access-am3-configs/app/install_ancil/rose-app.conf`

This file links the working copies of the model ancillaries to a curated set of inputs maintained by ACCESS-NRI under `/g/data/vk83/configurations/inputs/access-am3` and organised by modelling realm and/or configuration.

---

The majority of these files are generated using an external ancillary workflow, the use of which is beyond the scope of this training. However, some limited manual modification may be possible by first copying the target file to a space you control, modifying it, and editing the `install_ancil/rose-app.conf` file to point at your path.

<div class="note">Common mistakes such as date/calendar mismatches, incorrect grid specifications, and missing storage directives in the PBS script may prevent custom ancillaries from being accepted by the model.</div>

---

## Troubleshooting

Workflows are compilcated pieces of software with many components. The potential for error increases with workflow size and complexity, and each workflow may need to be debugged differently.

To debug a Cylc 8 workflow in a general sense, the following instructions may be useful.

---

## 1. Identify the task/job in which the error occurred.

To identify where in the workflow the failure occurred, either identify the task in the TUI which is reporting as "failed" or:

1. Navigate to the log directory:

  ```shell
  cd $WORKFLOW_DIR/runN/log
  ```

2. Navigate to the latest cycle point:

  ```shell
  cd $(ls | tail -1)
  ```

---

Within this directory are subdirectories for each of the jobs within the workflow. The last folder written is typically the location of the failure. To access the last attempted run of the job, execute the following command:

```shell
cd $(ls -t | head -1)/NN
```

This command will take you to the latest attempt of the most recently written job directory.

---

## 2. Diagnose the error

Depending on how the workflow has been designed, error messages can appear in either `job.err` or `job.out`, and be logged as "Error", "Warning", "Critical" etc. To get a general idea of what went wrong, you can try the following commands:

```shell
# Case insensitive searches for error/warning/critical
grep -i -E "error|warning|critical" job.err
grep -i -E "error|warning|critical" job.out
```

On many occasions, this is sufficient to identify the issue.

---

For example:

```shell
grep -i -E "error|warning|critical" job.err
??????????????????????????????      WARNING       ??????????????????????????????
?  Warning code: -10
?  Warning from routine: GET_ENV_VAR
?  Warning message: Environment variable ATMOS_COMP is not set.
?  Warning from processor: 0
?  Warning number: 0
??????????????????????????????      WARNING       ??????????????????????????????
?  Warning code: -10
?  Warning from routine: RCF_SMC_STRESS
?  Warning message: Input and output soil properties are identical.
?  Warning from processor: 0
?  Warning number: 1
```

If not, you will need to read the log with your favourite text editor (i.e. `vim`, `nano` etc.) to establish the nature and context of the error, including any stacktraces, which may point to further avenues to resolve your failure.

---

### 3. Digging deeper

If your error resides within the UM itself, you have the option to dig into the raw FORTRAN output from the model. This output is located in the following path:

```
$WORKFLOW_DIR/runN/work/[TIMESTAMP]/atmos_main/pe_output
```

Within this directory are the following files:
- `am3.fort6.pe[NNN]` an output stream from a given processor
- `am3.fort6.pe.stdout` an overall output stream

These files may contain additional information to help diagnose your error. However, their interpretation is beyond the scope of this training.

---

# Summary

Today we have:
1. Connected to Gadi via a virtual terminal session
2. Configured Persistent Sessions
3. Checked out the n96e released configuration of ACCESS-AM3
4. Edited the model runtime and project allocations
5. Run the model
6. Explored the logs and output locations for the model

---

## Where to get help?

- General Help/Advice
  [ACCESS Hive Forum](https://forum.access-hive.org.au)
- Think you've found a bug? Raise an issue.
  [ACCESS AM3 Configs](https://github.com/ACCESS-NRI/access-am3-configs)