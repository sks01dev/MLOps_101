# Day 2: JupyterLab Configuration & Troubleshooting
### The Task
The objective was to diagnose and fix a JupyterLab server that was incorrectly configured. I had to ensure the server was accessible over the network and had the correct file system permissions to operate.
### The Problem
The server was failing due to two specific configuration gaps:
 * **Network Isolation:** It was bound to localhost, making it unreachable from the browser.
 * **Path Error:** The designated storage folder for notebooks did not exist on the disk.
### The Solution
**1. Create the Workspace**
Created the required directory so the server has a physical location to save files.
```bash
mkdir -p /root/notebooks/

```
**2. Update Server Configuration**
Modified jupyter_lab_config.py with the following parameters to enable remote access:
 * **IP:** Set to 0.0.0.0 to listen on all network interfaces.
 * **Port:** Set to 8888 to match the expected entry point for traffic.
 * **Directory:** Defined /root/notebooks/ as the root workspace.
**3. Execution**
Activated the virtual environment and launched the server using the corrected configuration file.
```bash
source /root/code/ml-env/bin/activate
jupyter lab --config=/root/code/jupyter_lab_config.py --allow-root

```
### Summary of Day 2
 * **Config over Commands:** Learned to use configuration files (.py) for persistent settings rather than typing long flags every time.
 * **Network Binding:** Understood that 0.0.0.0 is essential for making a server "extroverted" and accessible across a network.
