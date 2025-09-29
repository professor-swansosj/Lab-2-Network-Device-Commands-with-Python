# Instructions — Lab 2 — Network Device Commands with Python

> **Before you begin:** Confirm the Always-On sandbox is online and note the current hostname/IP, SSH port, and default credentials from DevNet. Inside the dev container, verify DNS and outbound network access (`ping`, `curl ifconfig.me`).


Follow these steps in order.

> **Logging Requirement:** Write progress to `logs/lab.log` as you complete each step.

## Step 1 — Clone the Repository
**Goal:** Getting your own copy of the starter repo.

**What to do:**  
Clone the repo and change into the directory so GitHub Classroom can grade your work in-place.
(See your assignment link for the correct URL.)


**You’re done when:**  
- You are in the new folder (`pwd` shows the repo path).
- `git status` shows you’re on the default branch with no local changes.


**Log marker to add:**  
`[LAB2_START]`

## Step 2 — Open a Dev Container
**Goal:** Open a consistent dev environment (Python libs preinstalled).

**What to do:**  
Reopen in Container and wait for the first-time build to finish. Then review the health files.


**You’re done when:**  
- `logs/DEVCONTAINER_STATUS.txt` shows `Overall status: READY`.
- `logs/devcontainer_health.log` shows DNS_OK / NET_OK / PKG_OK lines.


**Log marker to add:**  
`[DEVCONTAINER_OK]`

## Step 3 — Confirm Sandbox Target
**Goal:** Identify the exact device and verify reachability from the container.

**What to do:**  
Note the host/IP, port, username, and password from DevNet. Test name resolution and ping from inside the container.


**You’re done when:**  
- You can reach the host/IP (ping succeeds or IP reachable).
- You can SSH interactively (optional sanity check).


**Log marker to add:**  
`[SANDBOX_READY]`

## Step 4 — Explore the Library Surface Area
**Goal:** Discover the connection class, device types, and send-command method.

**What to do:**  
In `python`, use `dir()`, `help()`, and `inspect` to explore Netmiko and identify the correct `device_type`.


**You’re done when:**  
- You can name the connection class you’ll use.
- You can state the correct device type string for IOS/IOS-XE.
- You know which method sends a command to the device.


**Log marker to add:**  
`[STEP_DONE]`

## Step 5 — Establish Connection & Run a Show Command
**Goal:** Build your first script that connects and runs one command.

**What to do:**  
Create `src/connect_basic.py`. Prompt for creds, build the device dict, connect, run `show ip interface brief`,
print output, and save raw output to `data/raw/show_ip_int_brief.txt`. Log events to `logs/connect_basic.log`.


**You’re done when:**  
- You see command output in the terminal.
- `data/raw/show_ip_int_brief.txt` is non-empty.
- `logs/connect_basic.log` shows CONNECT_OK, CMD=..., RAW_SAVED=...


**Log marker to add:**  
`[CONNECT_OK]`

## Step 6 — Add Error Handling
**Goal:** Handle bad creds, timeouts, and SSH failures gracefully.

**What to do:**  
Create `src/connect_with_errors.py`. Wrap connect/command in try/except; print clear messages; log an exit-with-error line.


**You’re done when:**  
- A friendly error shows for bad password and for unreachable host.
- `logs/connect_with_errors.log` shows one of: AuthenticationException / NetMikoTimeoutException / SSHException and EXIT_WITH_ERROR.


**Log marker to add:**  
`[EXIT_WITH_ERROR]`

## Step 7 — Automate Loopback Configuration
**Goal:** Push small, repeatable config from structured data and verify.

**What to do:**  
Create `src/add_loopbacks.py`. Iterate a list of dictionaries to add Loopbacks, verify via show command,
and save verification to `data/raw/verify_loopbacks.txt`. Log one line per interface.


**You’re done when:**  
- Loopbacks appear in device output.
- `data/raw/verify_loopbacks.txt` exists and is non-empty.
- `logs/add_loopbacks.log` shows CFG_APPLIED lines and a VERIFY_OK line.


**Log marker to add:**  
`[CFG_APPLIED count=<n>]`

## Step 8 — Parse CLI Output with ntc-templates
**Goal:** Convert raw CLI to structured data with TextFSM templates.

**What to do:**  
Create `src/parse_and_report.py`. Run show commands and parse with ntc-templates; pretty-print the structured data.


**You’re done when:**  
- Terminal shows structured data.
- `logs/parse_and_report.log` shows PARSE_OK and PPRINT_OK.


**Log marker to add:**  
`[PARSE_OK command="show ip interface brief"]`

## Step 9 — Generate a Short Report
**Goal:** Produce a readable summary file.

**What to do:**  
In the same script, build a brief report (hostname, model, uptime, up/up count) and save to `data/reports/device_report.txt`.


**You’re done when:**  
- `data/reports/device_report.txt` exists with the key fields.
- `logs/parse_and_report.log` shows REPORT_SAVED=...


**Log marker to add:**  
`[REPORT_SAVED=data/reports/device_report.txt]`

## Step 10 — Commit, Push, Verify
**Goal:** Submit and verify in GitHub Classroom.

**What to do:**  
Commit all changes and push. Open the Actions tab in your repo and review the autograder run.


**You’re done when:**  
- Actions shows green for this lab.
- Repo contains all required scripts, raw outputs, report, and logs; health files show READY.


**Log marker to add:**  
`[LAB2_END]`


## Submission Checklist
- [ ] Logs show start/end and required markers without secrets.
- [ ] Raw output saved for `show ip interface brief`.
- [ ] Loopbacks configured and verified with saved output.
- [ ] Parsed data printed and included in the report.
- [ ] `data/reports/device_report.txt` exists and includes hostname/model/uptime.
