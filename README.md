# MacOS on ProxMox

Run **macOS as a virtual machine (VM) on Proxmox VE** with this step‑by‑step guide.  
This repository provides detailed installation and configuration instructions to successfully deploy macOS on your Proxmox environment.

---

## ✨ Features
- Tested installation process for macOS versions (Big Sur → Sonoma)  
- Complete VM configuration steps for CPU, RAM, disk, and EFI  
- Workarounds for networking, GPU passthrough, and performance tuning  
- Troubleshooting tips for common installation issues  

---

## 📋 Requirements
Before you begin, ensure you have:

- **Proxmox VE** (latest stable release recommended)  
- **x86_64 CPU** with virtualization (Intel VT‑x / AMD‑V enabled in BIOS)  
- **8GB RAM** (minimum recommended for macOS VM)  
- **40GB+ storage** available for the macOS guest  
- Access to a **macOS recovery or installer image**  

---

## 🚀 Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/RedHatRanger/MacOS_on_ProxMox.git
   cd MacOS_on_ProxMox
   ```

2. Follow the steps in the detailed [Guide.md](./Guide.md).

3. Start your VM in Proxmox once configuration is complete.

---

## 📖 Documentation

The complete installation instructions can be found here:  
👉 [Guide.md](./Guide.md)

Contents include:
- Preparing Proxmox and enabling required features  
- Creating and configuring the macOS VM  
- Adjusting CPU flags and machine type  
- Optimizing disk and network settings  
- Post‑installation tweaks  

---

## 🔧 Troubleshooting
Common issues addressed in the guide:
- Stuck boot process or kernel errors  
- Display or GPU passthrough failures  
- Networking not detected  
- Installation reboots unexpectedly  

⚠️ If you encounter persistent issues, please check [Proxmox forums](https://forum.proxmox.com/) or the [Issues](https://github.com/RedHatRanger/MacOS_on_ProxMox/issues) section.

---

## 📜 Disclaimer
- Running macOS on non‑Apple hardware may violate Apple’s **End User License Agreement (EULA)**.  
- This repository is provided for **educational and experimental use only**.  
- The author and contributors are **not responsible** for any damages, data loss, or license violations.  
- Proceed at your own risk.  

---

## 🤝 Contributing
Contributions are welcome!  
- Open an [issue](https://github.com/RedHatRanger/MacOS_on_ProxMox/issues) for bugs, feature requests, or questions.  
- Submit a pull request to improve the guide or documentation.  

---

## 🙌 Credits
- **Author:** [RedHatRanger](https://github.com/RedHatRanger)  
- Thanks to the Proxmox and Hackintosh communities for ongoing support and research.  

---

## 📄 License
This project is released under the **MIT License**.  
See [LICENSE](./LICENSE) for details.

---

👉 Would you like me to **add GitHub badges** (Proxmox version, license, stars/forks) at the top to make it look even more professional?
