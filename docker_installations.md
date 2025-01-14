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

1. **Uninstall Old Docker Versions** (if any)
   ```bash
   sudo apt-get remove docker docker-engine docker.io containerd runc
   ```

2. **Set Up Repository**  
   ```bash
   sudo apt-get update
   sudo apt-get install \
       apt-transport-https \
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

3. **Install Docker Engine**  
   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

4. **Manage Docker as a Non-Root User** (Optional)
   ```bash
   sudo usermod -aG docker $USER
   ```
   - Log out and back in for the group changes to apply, or run `newgrp docker`.

5. **Verify**  
   ```bash
   docker --version
   docker run hello-world
   ```

> **Docker Compose**:  
> For Compose v2 (recommended), install the **docker** package which includes the `docker compose` subcommand. If you need the older `docker-compose` v1, install it separately:
> ```bash
> sudo apt-get install docker-compose
> ```

---

## Quick Verification

After installing on any platform, you can run:

```bash
docker run hello-world
```

If the command executes successfully and outputs “Hello from Docker!”, Docker is installed and operational.

---

### Additional References

- **Docker Docs**: [docs.docker.com](https://docs.docker.com/)  
- **macOS**: [Install Docker Desktop on Mac](https://docs.docker.com/desktop/install/mac-install/)  
- **Windows**: [Install Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/)  
- **Ubuntu**: [Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

These steps should get you up and running with Docker on your preferred platform. If you encounter issues, check Docker’s official troubleshooting guides or consult community forums for help.