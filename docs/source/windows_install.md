# How to Set Up LeRobot in Windows

So you want to use LeRobot in Windows? This guide walks you through setting up LeRobot and connecting it to the SO-100 robot from Windows using WSL and Docker.

---

## 📦 What You'll Need

- A Windows 10 (version 2004+) or Windows 11 computer
- An SO-100 robot
- Admin access on your machine

---

## 🧭 Overview

1. [Install WSL (Windows Subsystem for Linux)](https://learn.microsoft.com/en-us/windows/wsl/install)
2. [Install Docker Desktop (Windows version)](https://www.docker.com/get-started/)
3. Install LeRobot via:
   - [Docker image (preferred)](https://github.com/huggingface/lerobot/tree/main/docker)
   - [Source (if Docker image is outdated)](https://huggingface.co/docs/lerobot/installation)
4. Connect USB device to WSL
5. Run the robot in Docker

---

## 🛠 Step-by-Step Instructions

### 1. Install WSL

> ⚠️ You must be using Windows 10 (2004+) or Windows 11. Upgrading to Windows 11 is strongly recommended.

1. Open PowerShell as **Administrator**.
2. Run:

   ```powershell
   wsl --install
   ```

3. Use the default (Ubuntu) distro.
4. Once installed, run `wsl` to launch Ubuntu.
5. Set up a username and password.

> Forgot your password?
>
> - Open PowerShell as Administrator.
> - Launch as root:
>   ```powershell
>   wsl -u root
>   ```
> - Reset your password:
>   ```bash
>   passwd <yourusername>
>   ```
> - Type `exit`, then test the new password by running a sudo command in WSL.

---

### 2. Install Docker Desktop

> 🔴 Do **not** install Docker inside WSL. Use **Docker Desktop for Windows**.

1. [Download Docker Desktop](https://www.docker.com/get-started/)
2. Choose the version based on your system:
   - `x64-based PC` → **AMD64**
   - `ARM-based processor` → **ARM64**

> To check your system type:
> - Open "System Information" from the Start Menu
> - Look under **System Type**

3. Install Docker Desktop.
4. Launch it.
5. In the Docker search bar, search for:
   - `huggingface/lerobot-cpu`
   - or `huggingface/lerobot-gpu`
6. Click **Pull** (don’t use "Run"—you'll run the container from WSL later).

---

### 3. Share USB with WSL

We'll use `usbipd-win` to connect the SO-100 USB controller to WSL.

#### Install USBIP Tools

1. Open PowerShell as Administrator.
2. Install usbipd:

   ```powershell
   winget install --interactive --exact dorssel.usbipd-win
   ```

#### Find and Attach the USB Device

1. Plug in the SO-100.
2. Run:

   ```powershell
   usbipd list
   ```

3. Find your USB device by **BUSID** (e.g. `1-3`).

> Not sure which is the right device?  
> Unplug the controller, rerun `usbipd list`, and compare results.

4. Share and attach the USB:

   ```powershell
   usbipd bind --busid <busid>
   usbipd attach --wsl --busid <busid>
   ```

> ⚠️ Re-attaching is required if the USB is unplugged or the controller is powered off.  
> Windows may assign a new BUSID each time.

---

### 4. Identify Port ID in WSL

Once the USB is attached, find the controller in WSL:

```bash
cd /dev
ls > unplugged.txt
# Now plug in the controller
ls > plugged.txt
diff unplugged.txt plugged.txt
```

This will show the new device, typically something like `/dev/ttyACM0`.

> ✅ This port ID stays consistent unless WSL is reset or the USB is unplugged.

---

### 5. Run LeRobot in Docker via WSL

Now you're ready to run LeRobot!

Use the port ID (`ttyACM0`) you identified above when starting LeRobot from the Docker container.

📘 Refer to the official [SO-100 setup docs](https://huggingface.co/docs/lerobot/so100) for final configuration.

> ❌ Ignore prompts to "find USB ports for each arm." Use the port ID you already found.

---

## ✅ You're Ready to Go!

You’ve now:
- Installed WSL and Docker
- Pulled the LeRobot image
- Connected the SO-100 controller
- Identified the correct USB port

Time to play with your robot 🚀
