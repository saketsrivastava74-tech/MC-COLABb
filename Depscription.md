```txt
███╗   ███╗ ██████╗       ██████╗ ██████╗ ██╗      █████╗ ██████╗ ██████╗ 
████╗ ████║██╔════╝      ██╔════╝██╔═══██╗██║     ██╔══██╗██╔══██╗██╔══██╗
██╔████╔██║██║           ██║     ██║   ██║██║     ███████║██████╔╝██████╔╝
██║╚██╔╝██║██║           ██║     ██║   ██║██║     ██╔══██║██╔══██╗██╔══██╗
██║ ╚═╝ ██║╚██████╗      ╚██████╗╚██████╔╝███████╗██║  ██║██████╔╝██████╔╝
╚═╝     ╚═╝ ╚═════╝       ╚═════╝ ╚═════╝ ╚══════╝╚═╝  ╚═╝╚═════╝ ╚═════╝
```

Production-grade Minecraft Hosting Panel for Google Colab.

Run optimized Minecraft servers directly inside Google Colab with persistent Google Drive storage, live console support, automatic installation, and performance tuning.

---

# Features

* Google Drive Persistent Hosting
* One-click Server Installation
* Live Real-Time Console
* Automatic Java Installation
* Optimized JVM Flags
* TPS + Chunk Loading Optimizations
* Multi-Software Support
* RAM Allocation System
* Anti-Colab Timeout System
* Beginner-Friendly UI Panel
* Real-Time Command Execution
* Automatic Server Folder Management

---

# Supported Software

| Software | Supported Versions |
| -------- | ------------------ |
| Paper    | 1.8.8 → Latest     |
| Purpur   | Modern Versions    |
| Fabric   | Most Versions      |
| Vanilla  | Most Versions      |

---

# Recommended Minecraft Versions

* 1.16.5
* 1.18.2
* 1.20.1
* 1.21.x

---

# Performance Optimizations

MC-COLABb automatically applies:

* Optimized Aikar JVM Flags
* Reduced Chunk Loading Lag
* Better TPS Stability
* Lower Memory Usage
* Faster World Generation
* Async Optimization Tweaks
* Optimized Entity Activation Ranges
* Reduced Tick Lag

---

# Server Storage Location

All servers are stored permanently inside Google Drive:

```bash
/content/drive/MyDrive/MinecraftServers
```

Example:

```bash
MinecraftServers/
 ├── SurvivalSMP/
 ├── Lifesteal/
 └── Practice/
```

---

# Installation

## 1. Open Google Colab

https://colab.research.google.com

---

## 2. Create a New Notebook

* File → New Notebook

---

## 3. Paste Modules

Paste:

* MODULE 1
* MODULE 2
* MODULE 3

into separate Colab cells.

Run them in order.

---

# Usage

## Install Server

Go to:

```txt
Installer Tab
```

Fill:

* Server Name
* Software
* Version

Then click:

```txt
Install Server
```

---

## Start Server

Go to:

```txt
Dashboard Tab
```

Select:

* Server
* RAM Allocation

Then click:

```txt
Start
```

---

# Console Commands

You can send commands live:

```txt
say hello
op YourName
gamemode creative YourName
```

---

# Plugins

Put plugins inside:

```txt
plugins/
```

---

# Mods

For Fabric servers:

```txt
mods/
```

---

# Colab Limitations

Google Colab Free has some limitations:

* Sessions may disconnect
* Runtime resets possible
* ~12 hour runtime limit
* Limited CPU resources

The project includes an anti-timeout system, but long-term 24/7 hosting is not guaranteed on free Colab.

---

# Recommended Configurations

| Use Case             | Recommended Setup |
| -------------------- | ----------------- |
| SMP                  | Purpur 1.21.1     |
| PvP                  | Paper 1.8.9       |
| Lightweight Survival | Paper 1.16.5      |
| Modded               | Fabric 1.20.1     |

---

# Planned Features

* Plugin Auto Installer
* One-click Geyser Support
* Automatic Backups
* Server Statistics Panel
* TPS Monitor
* World Pregeneration
* Modpack Installer
* Multi-Server Runtime Support

---

# Support

If you like the project:

* Star the repository
* Fork the project
* Contribute improvements
* Share with friends

---

# MC-COLABb

Cloud Minecraft Hosting directly from Google Colab.
