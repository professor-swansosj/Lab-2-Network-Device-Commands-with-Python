# Lab 2 — Network Device Commands with Python

**Course:** Software Defined Networking  
**Module:** Network Automation Fundamentals • **Lab #:** 2  
**Estimated Time:** 90-120 Minutes

## Objectives
- Use Netmiko to establish an SSH session to a Cisco Catalyst 9K in the Cisco DevNet Always-On Sandbox.
- Collect credentials securely at runtime using input() and getpass.getpass().
- Execute basic IOS “show” commands and capture output to files.
- Implement Python error handling (try/except) for connection and command failures.
- Push simple configuration (loopback interfaces) from Python using iterable data structures.
- Parse unstructured CLI output into structured data with ntc-templates (TextFSM).
- Generate a simple, formatted report using f-strings (or Jinja2, optional).

## Prerequisites
- Python 3.11 (via the provided dev container)
- Accounts: GitHub, Cisco DevNet
- Devices/Sandboxes: Cisco DevNet Always-On Catalyst (IOS-XE/9K)

## Overview
In this lab you’ll connect to a Cisco DevNet Always-On Catalyst device using Netmiko, run read-only “show” commands, push a small loopback config, then parse CLI output with ntc-templates to produce a short report. The emphasis is on safe automation patterns: prompting for credentials, error handling, structured data, and clean logging.


## Resources
- [Cisco DevNet Always-On Sandboxes](https://developer.cisco.com/site/sandbox/) — Find the Catalyst IOS-XE device details here.- [Netmiko Documentation](https://ktbyers.github.io/netmiko/)- [NTC Templates (TextFSM)](https://github.com/networktocode/ntc-templates) — Match exact command strings to template names.

## FAQ
**Q:** The device type confuses me—`cisco_ios` or `cisco_xe`?  
**A:** Use `cisco_ios` for most IOS-XE Catalyst devices with Netmiko; verify by checking Netmiko’s supported types.

**Q:** Parsing returns empty lists—what gives?  
**A:** Ensure the command string matches the template exactly (e.g., `show ip interface brief`, not an alias).

**Q:** Where do logs and outputs go?  
**A:** Logs under `logs/`, raw CLI under `data/raw/`, and your final report under `data/reports/`.



## Deliverables
- README and INSTRUCTIONS standardized; logs and artifacts present
- Scripts complete; required log markers present; autograder passes
- Grading: **75 points**

## Grading Breakdown
| Step | Requirement | Points |
|---|---|---|
| 1. Setup | Container opens and packages installed | 5 |
| 2. Explore library | Show methods or details from Netmiko | 5 |
| 3. Secure login | Use input for username and hidden password | 4 |
| 3. Secure login | Device dictionary has type, host, username, password | 4 |
| 4. Connect | Successful connection made to device | 5 |
| 5. Show command | Run a show command and save raw output to file | 5 |
| 6. Error handling | Script shows clear error message for bad login or timeout | 8 |
| 7. Loopback config | Add loopback from list of dictionaries | 8 |
| 7. Loopback config | Show command verifies loopbacks and saves to file | 6 |
| 8. Parse output | Use templates to parse show command into structured data | 7 |
| 8. Parse output | Print structured data in clear format | 3 |
| 9. Report | Create and save formatted report with key device details | 10 |
| All steps | Logs created with start and end, no secrets shown | 5 |
| **Total** |  | **75** |

## 🔧 Troubleshooting & Pro Tips
**Dev Container Didn’t Install Dependencies**  
*Symptom:* ModuleNotFoundError for netmiko or ntc_templates when running code.  
*Fix:* Open in devcontainer; verify with `pip list`. If missing, run `pip install -r requirements.txt`.

**Wrong Hostname or IP Address**  
*Symptom:* Connection times out or immediately fails.  
*Fix:* Use the exact host/IP from the DevNet page. If hostname fails, try the raw IP; verify DNS with `ping`.

**Incorrect Device Type**  
*Symptom:* Login fails or 'Authentication to device failed'.  
*Fix:* Confirm exact `device_type` from Netmiko (e.g., `cisco_ios` / `cisco_xe`). Check with `dir(netmiko)`.

**Hard-Coded Credentials**  
*Symptom:* Secrets visible in Git history.  
*Fix:* Use `input()` and `getpass.getpass()`; rotate credentials if accidentally committed.

**Empty Parsed Data**  
*Symptom:* `parse_output()` returns [] or IndexError.  
*Fix:* Match the template’s expected command exactly (e.g., `show ip interface brief`).

**File Writing Errors**  
*Symptom:* FileNotFoundError or empty outputs.  
*Fix:* Write to the correct relative paths under `data/` and `logs/`.



## Autograder Notes
- Log file: `logs/lab.log`
- Required markers: `LAB2_START`, `DEVCONTAINER_OK`, `PKG_OK: netmiko`, `PKG_OK: ntc-templates`, `CONNECT_OK`, `CMD=show ip interface brief`, `RAW_SAVED`, `ERR=AuthenticationException`, `ERR=NetMikoTimeoutException`, `ERR=SSHException`, `CFG_APPLIED`, `VERIFY_OK`, `PARSE_OK platform=cisco command="show ip interface brief"`, `PPRINT_OK`, `REPORT_SAVED`, `LAB2_END`

## License
© 2025 Your Name — Classroom use.