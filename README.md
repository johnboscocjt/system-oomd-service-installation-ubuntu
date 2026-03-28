# systemd-oomd Installation Guide for Ubuntu Linux

**systemd-oomd** — A userspace out-of-memory (OOM) killer that helps prevent system freezes by proactively terminating memory-hungry processes.

---

## 📖 Table of Contents
- [What is systemd-oomd?](#what-is-systemd-oomd)
- [Prerequisites](#prerequisites)
- [Installation Steps](#installation-steps)
- [Verification](#verification)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)

---

## 🚀 What is systemd-oomd?

**systemd-oomd** is a userspace out-of-memory killer that monitors system pressure and proactively terminates memory-hungry processes before the system becomes unresponsive. Unlike traditional OOM killers that only act when memory is completely exhausted, systemd-oomd uses:

- **Pressure Stall Information (PSI):** Monitors memory, CPU, and I/O pressure
- **Proactive Termination:** Kills processes before system freezes occur
- **Managed Services:** Integrates seamlessly with systemd-managed services

### Availability
- Ubuntu 20.04+: Available in default repositories
- Ubuntu 22.04 LTS+: Enabled by default

---

## 📋 Prerequisites

### Kernel Support (PSI)

systemd-oomd requires the kernel to have **Pressure Stall Information (PSI)** support enabled. This is enabled by default on most modern Ubuntu kernels (20.04+).

**Check if PSI is available:**
```bash
cat /proc/pressure/memory
```

**Expected output:**
```
some avg10=0.00 avg60=0.00 avg300=0.00 total=0
full avg10=0.00 avg60=0.00 avg300=0.00 total=0
```

If you receive `No such file or directory`, PSI is not enabled. See the [Troubleshooting](#troubleshooting) section for resolution.

---

## 🔧 Installation Steps

### Step 1: Update Package Lists
```bash
sudo apt update
```

### Step 2: Install systemd-oomd
```bash
sudo apt install systemd-oomd
```

### Step 3: Enable and Start the Service
Enable automatic startup at boot and start the service immediately:
```bash
sudo systemctl enable --now systemd-oomd.service
```

### Step 4: Verify Service Status
```bash
sudo systemctl status systemd-oomd.service
```

Expected output should show `active (running)`.

---

## ✅ Verification

### Check if systemd-oomd is Running
```bash
systemctl status systemd-oomd
```

### Monitor OOM Events
View real-time logs of memory pressure and any actions taken:
```bash
journalctl -u systemd-oomd -f
```

### View Recent OOM Actions
```bash
journalctl -u systemd-oomd --since "1 hour ago"
```

### List Managed Services
```bash
systemd-oomd --user --status
```

---

## ⚙️ Configuration

### Default Configuration Location
Configuration files are located at:
- `/etc/systemd/oomd.conf` — System-wide configuration
- `~/.config/systemd/oomd.conf` — User-specific configuration

### Key Configuration Options

| Option | Description | Default |
|--------|-------------|---------|
| `SwapUsedLimit` | Maximum swap usage before action | `95%` |
| `DefaultMemoryPressureLimit` | Memory pressure threshold | `80%` |
| `DefaultMemoryPressureDurationSec` | Duration pressure must exceed limit | `30s` |

### Edit Configuration
```bash
sudo nano /etc/systemd/oomd.conf
```

After making changes, restart the service:
```bash
sudo systemctl restart systemd-oomd.service
```

---

## 🔍 Troubleshooting

| Issue | Solution |
|-------|----------|
| **PSI not available** (`/proc/pressure/memory` missing) | Enable PSI by adding `psi=1` to GRUB command line:<br>1. Edit `/etc/default/grub`<br>2. Add `psi=1` to `GRUB_CMDLINE_LINUX_DEFAULT`<br>3. Run `sudo update-grub`<br>4. Reboot |
| **Service fails to start** | Check logs: `journalctl -u systemd-oomd -xe`<br>Verify kernel version: `uname -r` (requires 5.0+) |
| **Too aggressive killing** | Adjust thresholds in `/etc/systemd/oomd.conf`:<br>`DefaultMemoryPressureLimit=90%`<br>`DefaultMemoryPressureDurationSec=60s` |
| **Not killing any processes** | Check if managed services are properly configured:<br>`systemd-oomd --user --status`<br>Ensure processes are managed by systemd |
| **Package not found** | Ensure you're on Ubuntu 20.04+:<br>`lsb_release -a`<br>Update package lists: `sudo apt update` |

---

## 📝 Additional Resources

- [Official systemd-oomd Documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd-oomd.service.html)
- [Ubuntu systemd-oomd Wiki](https://wiki.ubuntu.com/systemd-oomd)

---

## 📄 Documentation Maintained By

*Community Contribution*

---

*For issues or questions, please refer to the Ubuntu forums or systemd documentation.*
```

This structure provides:
- Clear hierarchy with emoji headers for visual scanning
- A table of contents for quick navigation
- Step-by-step installation instructions
- Verification methods with expected outputs
- Configuration guidance
- A comprehensive troubleshooting table
- Proper command formatting with syntax highlighting indicators
