Below are brief instructions for installing Docker on **macOS**, **Windows**, and **Ubuntu**. For more detailed or up-to-date information, consult the official [Docker documentation](https://docs.docker.com/).

---

## Install Docker on macOS

1. **Check System Requirements**  
   - macOS must be 10.14 or newer (for Intel) or 11+ (for Apple Silicon).
   - Virtualization enabled (usually by default on macOS).

2. **Download Docker Desktop**  
   - Go to the [Docker Desktop for Mac page](https://docs.docker.com/desktop/install/mac-install/).
   - Download the `.dmg` installer for your architecture (Intel or Apple Silicon).

3. **Install & Launch**  
   - Double-click the downloaded `.dmg` file.
   - Drag and drop the **Docker.app** icon to your **Applications** folder.
   - Open **Docker.app** from **Applications** or Launchpad.
   - Wait for Docker Desktop to finish starting (the whale icon in your menu bar turns stable).

4. **Verify**  
   - Open a **new** terminal.
   - Run `docker --version` to confirm Docker is installed and running.

> **Alternative (Open-Source)**  
> If you don’t want to use Docker Desktop, you can install Docker Engine alternatives (like **Colima** or **Rancher Desktop**). Refer to their respective docs for installation steps.

---

## Install Docker on Windows

1. **Check System Requirements**  
   - Windows 10 (Professional or Enterprise) or Windows 11.  
   - Hardware virtualization enabled in BIOS/UEFI.  
   - **WSL 2** installed if you want best performance ([WSL instructions](https://docs.microsoft.com/windows/wsl/install)).

2. **Download Docker Desktop**  
   - Go to the [Docker Desktop for Windows page](https://docs.docker.com/desktop/install/windows-install/).
   - Download the installer (`.exe` file).

3. **Install & Configure**  
   - Double-click the `.exe` installer.
   - Follow the wizard, enabling features such as “Use WSL 2 instead of Hyper-V” if desired.
   - Once done, Docker Desktop will launch automatically.

4. **Verify**  
   - Open **PowerShell** or **Command Prompt**.
   - Run `docker --version` to confirm installation.
   - Optionally check `docker run hello-world` to confirm Docker is working.

> **Note on Windows Home**  
> On Windows Home editions, you must use **WSL 2** for Docker Desktop to run properly.

---

## Install Docker on Ubuntu

## 1. Install Docker Compose v2 (Recommended)
Docker Compose **v2** is now distributed as a plugin alongside the Docker Engine. This is the preferred approach going forward.
### 1.1 Install Docker Engine
1. **Uninstall old Docker versions** (if any):
   ```bash
   sudo apt-get remove docker docker-engine docker.io containerd runc
   ```
2. **Set up the Docker official repository**:
   ```bash
   sudo apt-get update
   sudo apt-get install \
     ca-certificates \
     curl \
     gnupg \
     lsb-release
   ```
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
3. **Install Docker Engine**:
   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```
### 1.2 Install the Docker Compose v2 Plugin
1. **Install the plugin**:
   ```bash
   sudo apt-get install docker-compose-plugin
   ```
2. **Verify**:
   ```bash
   docker compose version
   ```
   You should see something like:
   ```
   Docker Compose version v2.x.x
   ```
> **Usage**: Now, you’ll run Compose commands as:
> ```bash
> docker compose up
> docker compose down
> ```
> rather than `docker-compose up`.
---
---
## Post-Installation (Applies to Both Methods)
1. **Manage Docker as a Non-Root User**:
   By default, you need `sudo` for `docker` commands. If you want to run Docker/Compose without `sudo`:
   ```bash
   sudo usermod -aG docker $USER
   ```
   Then **log out and log back in** (or run `newgrp docker`).
2. **Test with a Simple Project**:
   ```bash
   mkdir test-compose && cd test-compose
   ```
   Create a `docker-compose.yml` (or `compose.yaml`) like:
   ```yaml
   services:
     hello:
       image: hello-world
   ```
   Then run:
   - **Compose v2**:
     ```bash
     docker compose up
     ```
   - **Compose v1**:
     ```bash
     docker-compose up
     ```
3. **Check Running Containers**:
   ```bash
   docker ps
   ```
   (or `docker container ls`)
---
### Additional References

- **Docker Docs**: [docs.docker.com](https://docs.docker.com/)  
- **macOS**: [Install Docker Desktop on Mac](https://docs.docker.com/desktop/install/mac-install/)  
- **Windows**: [Install Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/)  
- **Ubuntu**: [Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

These steps should get you up and running with Docker on your preferred platform. If you encounter issues, check Docker’s official troubleshooting guides or consult community forums for help.


# Known Issue when starting docker compose for Ubuntu
When you see the message:
```
Your kernel does not support swap limit capabilities or the cgroup is not mounted. Memory limited without swap.
```
…it usually means **Docker** cannot apply swap memory limits in containers on your current system—either because:
1. The **kernel** was built without `CONFIG_MEMCG_SWAP` support, **or**
2. The **cgroup memory subsystem** (particularly for swap accounting) isn’t enabled or mounted.
Although this doesn’t prevent Docker from running, it means you **cannot** set or enforce swap limits (`--memory-swap` flags) on containers. If that’s acceptable, you can ignore the warning. If you want **full memory + swap limit support**, here’s how to address it:
---
## 1. Verify If You Really Need Swap Limits
- **If you don’t explicitly use `--memory-swap`** or have no need to strictly manage container swap usage, you can ignore this warning.
- **If you do** need swap-limiting for production or tight resource constraints, read on.
---
## 2. Enable the Memory cgroup Subsystem
Depending on your distro and boot configuration, you might need to enable the memory cgroup at boot. Typically, you do this by adding kernel parameters in your bootloader configuration (e.g., GRUB).
### Example (Ubuntu / Debian)
1. **Edit** your GRUB configuration file:
   ```bash
   sudo nano /etc/default/grub
   ```
2. **Append** the following to the `GRUB_CMDLINE_LINUX_DEFAULT` line:
   ```
   cgroup_enable=memory swapaccount=1
   ```
   For example, it might become:
   ```
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash cgroup_enable=memory swapaccount=1"
   ```
3. **Update GRUB** and reboot:
   ```bash
   sudo update-grub
   sudo reboot
   ```
After reboot, run:
```bash
docker info | grep 'Cgroup'
```
You should see references to memory cgroups being enabled, and the Docker warning about swap limit should go away.
---
## 3. Check if the Kernel Supports Memory Swap Accounting
Some distributions (or custom kernels) don’t compile in the necessary memory and swap cgroup features. You can check whether `CONFIG_MEMCG_SWAP` is enabled by looking in:
```bash
zcat /proc/config.gz | grep CONFIG_MEMCG_SWAP
```
*(If `/proc/config.gz` is not present, you can check your distro’s kernel config documentation or see if a config file is available in `/boot/`.)*
If `CONFIG_MEMCG_SWAP` is missing or set to `n`, you won’t have swap accounting without rebuilding or using a kernel that includes it.
---
## 4. If Using WSL 2 (on Windows)
Under WSL 2, Docker runs in a lightweight VM. By default, the VM’s kernel may have certain cgroup features disabled. You can control memory usage in the `.wslconfig` file and/or the Docker Desktop settings. However, if you see this warning in WSL 2, it’s often just telling you that swap limiting isn’t supported in that environment. You’d have to adjust your WSL 2 configuration or Docker Desktop’s settings—there isn’t always a straightforward fix if the underlying kernel was compiled without swap accounting.
---
## 5. Confirm the Warning Is Gone
After rebooting (or reconfiguring your environment), run:
```bash
docker run -it --rm --memory=128m --memory-swap=256m alpine sh
```
If Docker no longer logs the **swap limit** warning, your system is now honoring swap limits.
---
### Known Issue: Summary
- **Cause**: Your kernel/cgroup settings don’t allow Docker to enforce swap memory limits.
- **Quick Fix**: Enable memory cgroups and swap accounting at boot (e.g., `cgroup_enable=memory swapaccount=1`).
- **Kernel Support**: Ensure your kernel has `CONFIG_MEMCG_SWAP=y`.
- **Can Ignore?** If you don’t specifically need container swap limiting, you can ignore this warning. It won’t break Docker functionality otherwise.
With the above changes, Docker can limit container memory plus swap usage correctly. If you still see the warning, double-check your kernel’s config and that the `memory` cgroup is mounted.



