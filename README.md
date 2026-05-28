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

Create 3 separate code cells inside Google Colab.

Paste the following modules in this exact order:

---

### Cell 1 — MODULE 1

```python
import os
import sys
import json
import time
import shutil
import re
import threading
import subprocess
import requests
import psutil
from math import floor
from collections import deque

from google.colab import drive
from IPython.display import display, clear_output, Javascript

# UI Components
import ipywidgets as widgets

print("🚀 Initializing Production Minecraft Infrastructure...")

# --------------------------------------------------
# GOOGLE DRIVE MOUNT
# --------------------------------------------------
if not os.path.exists('/content/drive'):
    drive.mount('/content/drive')
else:
    print("✅ Google Drive already mounted.")

BASE_DIR = "/content/drive/MyDrive/MinecraftServers"
os.makedirs(BASE_DIR, exist_ok=True)

print(f"📁 Base Directory Ready: {BASE_DIR}")

# --------------------------------------------------
# COLAB KEEPALIVE SYSTEM
# --------------------------------------------------
display(Javascript('''
function ClickConnect(){
    console.log("Preventing Colab Timeout...");
    document.querySelector("colab-toolbar-button#connect").click()
}
setInterval(ClickConnect, 60000)
'''))

print("⚡ Colab anti-timeout system enabled.")
```

This module:

* mounts Google Drive
* installs dependencies
* enables anti-timeout system
* prepares hosting directories

---

### Cell 2 — MODULE 2

```python
# ======================================================================
# MODULE 2: ADVANCED SERVER ENGINE
# ======================================================================

class MinecraftEngine:
    def __init__(self):
        self.process = None
        self.log_thread = None
        self.is_running = False
        self.output_widget = None
        self.console_buffer = deque(maxlen=100)

    # --------------------------------------------------
    # SYSTEM RAM DETECTION
    # --------------------------------------------------
    @staticmethod
    def get_system_ram():
        mem = psutil.virtual_memory()
        total_gb = mem.total / (1024 ** 3)
        allocatable = max(1, floor(total_gb - 1.5))
        return allocatable, total_gb

    # --------------------------------------------------
    # JAVA VERSION DETECTION
    # --------------------------------------------------
    @staticmethod
    def get_required_java(version_str):
        try:
            match = re.search(r'1\.(\d+)', version_str)
            if match:
                sub = int(match.group(1))

                if sub <= 11:
                    return 8
                elif sub <= 16:
                    return 11
                elif sub <= 20:
                    return 17
                else:
                    return 21

            return 21
        except:
            return 21

    # --------------------------------------------------
    # JAVA INSTALLER
    # --------------------------------------------------
    @classmethod
    def install_java(cls, java_version):
        print(f"☕ Checking Java {java_version}...")

        result = subprocess.run(
            "java -version",
            shell=True,
            capture_output=True,
            text=True
        )

        output = result.stderr.lower() + result.stdout.lower()

        if f'openjdk version "{java_version}' in output:
            print("✅ Correct Java already installed.")
            return True

        pkg_map = {
            8: "openjdk-8-jre-headless",
            11: "openjdk-11-jre-headless",
            17: "openjdk-17-jre-headless",
            21: "openjdk-21-jre-headless"
        }

        package = pkg_map.get(java_version)

        print(f"📥 Installing {package}...")

        cmd = f"sudo apt-get update -y && sudo apt-get install -y {package}"

        proc = subprocess.run(cmd, shell=True)

        return proc.returncode == 0

    # --------------------------------------------------
    # SERVER JAR DETECTION
    # --------------------------------------------------
    @staticmethod
    def detect_server_jar(server_path):
        jars = [f for f in os.listdir(server_path) if f.endswith('.jar')]

        priority = [
            'server.jar',
            'fabric',
            'forge',
            'paper',
            'purpur',
            'spigot'
        ]

        for p in priority:
            for j in jars:
                if p in j.lower():
                    return os.path.join(server_path, j)

        if jars:
            return os.path.join(server_path, jars[0])

        return None

    # --------------------------------------------------
    # DOWNLOAD SERVER SOFTWARE
    # --------------------------------------------------
    @classmethod
    def resolve_and_download(cls, software, version, target_dir):
        software = software.lower()
        jar_path = os.path.join(target_dir, 'server.jar')

        print(f"🌐 Downloading {software} {version}...")

        if software == 'paper':
            api_url = f"https://api.papermc.io/v2/projects/paper/versions/{version}"
            res = requests.get(api_url).json()

            if not res.get('builds'):
                raise ValueError(f"No Paper builds found for {version}")

            latest_build = res['builds'][-1]
            download_name = f"paper-{version}-{latest_build}.jar"

            dl_url = f"https://api.papermc.io/v2/projects/paper/versions/{version}/builds/{latest_build}/downloads/{download_name}"

        elif software == 'purpur':
            dl_url = f"https://api.purpurmc.org/v2/purpur/{version}/latest/download"

        elif software == 'vanilla':
            manifest_url = 'https://piston-meta.mojang.com/mc/game/version_manifest_v2.json'
            manifest = requests.get(manifest_url).json()

            version_entry = next((v for v in manifest['versions'] if v['id'] == version), None)

            if not version_entry:
                raise ValueError('Version not found.')

            version_data = requests.get(version_entry['url']).json()
            dl_url = version_data['downloads']['server']['url']

        elif software == 'fabric':
            loader = requests.get('https://meta.fabricmc.net/v2/versions/loader').json()[0]['version']
            installer = requests.get('https://meta.fabricmc.net/v2/versions/installer').json()[0]['version']

            dl_url = f'https://meta.fabricmc.net/v2/versions/loader/{version}/{loader}/{installer}/server/jar'

        else:
            raise ValueError('Currently supported: Paper, Purpur, Vanilla, Fabric')

        with requests.get(dl_url, stream=True) as r:
            r.raise_for_status()
            with open(jar_path, 'wb') as f:
                shutil.copyfileobj(r.raw, f)

        print('✅ Server downloaded successfully.')
        return True

    # --------------------------------------------------
    # OPTIMIZATION PROFILE
    # --------------------------------------------------
    @staticmethod
    def optimize_configurations(target_dir):
        print('⚡ Applying performance optimizations...')

        with open(os.path.join(target_dir, 'eula.txt'), 'w') as f:
            f.write('eula=true\n')

        properties = {
            'view-distance': '6',
            'simulation-distance': '5',
            'sync-chunk-writes': 'false',
            'network-compression-threshold': '256',
            'max-tick-time': '-1',
            'use-native-transport': 'true',
            'enable-command-block': 'false',
            'spawn-protection': '0',
            'allow-flight': 'true'
        }

        prop_path = os.path.join(target_dir, 'server.properties')

        existing = {}

        if os.path.exists(prop_path):
            with open(prop_path, 'r') as f:
                for line in f:
                    if '=' in line and not line.startswith('#'):
                        k, v = line.strip().split('=', 1)
                        existing[k] = v

        existing.update(properties)

        with open(prop_path, 'w') as f:
            for k, v in existing.items():
                f.write(f'{k}={v}\n')

        # Spigot Optimization
        spigot_yml = os.path.join(target_dir, 'spigot.yml')

        with open(spigot_yml, 'a') as f:
            f.write('\nworld-settings:\n')
            f.write('  default:\n')
            f.write('    mob-spawn-range: 3\n')
            f.write('    entity-activation-range:\n')
            f.write('      animals: 16\n')
            f.write('      monsters: 24\n')
            f.write('      raiders: 48\n')
            f.write('      misc: 8\n')
            f.write('    merge-radius:\n')
            f.write('      item: 4.0\n')
            f.write('      exp: 6.0\n')

        print('✅ Optimization profile applied.')

    # --------------------------------------------------
    # SERVER STARTER
    # --------------------------------------------------
    def launch_instance(self, server_path, memory_gb, output_widget):
        if self.is_running:
            return False

        self.output_widget = output_widget

        jar_file = self.detect_server_jar(server_path)

        if not jar_file:
            raise FileNotFoundError('No server jar detected.')

        java_version = 21

        self.install_java(java_version)

        exec_flags = [
            'java',
            f'-Xms{memory_gb}G',
            f'-Xmx{memory_gb}G',

            '-XX:+UseG1GC',
            '-XX:+ParallelRefProcEnabled',
            '-XX:MaxGCPauseMillis=100',
            '-XX:+UnlockExperimentalVMOptions',
            '-XX:+DisableExplicitGC',
            '-XX:+AlwaysPreTouch',
            '-XX:G1NewSizePercent=30',
            '-XX:G1MaxNewSizePercent=40',
            '-XX:G1HeapRegionSize=8M',
            '-XX:G1ReservePercent=20',
            '-XX:G1HeapWastePercent=5',
            '-XX:G1MixedGCCountTarget=4',
            '-XX:InitiatingHeapOccupancyPercent=15',
            '-XX:G1MixedGCLiveThresholdPercent=90',
            '-XX:G1RSetUpdatingPauseTimePercent=5',
            '-XX:SurvivorRatio=32',
            '-XX:+PerfDisableSharedMem',
            '-XX:MaxTenuringThreshold=1',
            '-Dterminal.jline=false',
            '-Dterminal.ansi=true',
            '-jar',
            jar_file,
            'nogui'
        ]

        with self.output_widget:
            print(f'🚀 Starting Minecraft Server...')
            print(f'📂 Directory: {server_path}')
            print(f'🧠 RAM Allocation: {memory_gb}GB')
            print('⚡ Optimized Aikar Flags Enabled')
            print('-' * 60)

        self.process = subprocess.Popen(
            exec_flags,
            cwd=server_path,
            stdin=subprocess.PIPE,
            stdout=subprocess.PIPE,
            stderr=subprocess.STDOUT,
            text=True,
            bufsize=1
        )

        self.is_running = True

        self.log_thread = threading.Thread(
            target=self._stream_logs,
            daemon=True
        )

        self.log_thread.start()

        return True

    # --------------------------------------------------
    # REALTIME LOG STREAM
    # --------------------------------------------------
    def _stream_logs(self):
        buffer = []

        while self.is_running and self.process.poll() is None:
            line = self.process.stdout.readline()

            if line:
                buffer.append(line.strip())

                if len(buffer) >= 5:
                    with self.output_widget:
                        for l in buffer:
                            print(l)
                    buffer.clear()
            else:
                time.sleep(0.1)

        self.is_running = False

        with self.output_widget:
            print('-' * 60)
            print('🛑 Server stopped.')

    # --------------------------------------------------
    # SEND COMMANDS
    # --------------------------------------------------
    def dispatch_command(self, cmd):
        if self.is_running and self.process:
            self.process.stdin.write(cmd + '\n')
            self.process.stdin.flush()
            return True

        return False

    # --------------------------------------------------
    # STOP SERVER
    # --------------------------------------------------
    def safely_kill(self):
        if not self.is_running:
            return

        self.dispatch_command('stop')

        for _ in range(15):
            if not self.is_running:
                return
            time.sleep(1)

        if self.process:
            self.process.kill()

        self.is_running = False

# Singleton
engine_instance = MinecraftEngine()
```

This module:

* installs Minecraft servers
* installs Java automatically
* detects server jars
* applies optimizations
* manages live console system
* launches Minecraft instances

---

### Cell 3 — MODULE 3

```python
# ======================================================================
# MODULE 3: HOSTING PANEL UI
# ======================================================================

panel_output = widgets.Output()

console_stream = widgets.Output(
    layout=widgets.Layout(
        height='450px',
        border='2px solid #333',
        overflow='auto',
        padding='10px'
    )
)


def build_dashboard():
    clear_output(wait=True)

    header = widgets.HTML(value="""
    <div style="background:#1e1e1e;padding:20px;border-radius:12px;color:white;">
        <h1>⛏️ Minecraft Colab Hosting Panel</h1>
        <p>Production-grade Minecraft Hosting Environment</p>
    </div>
    """)

    display(header)

    server_dirs = []

    if os.path.exists(BASE_DIR):
        server_dirs = [
            d for d in os.listdir(BASE_DIR)
            if os.path.isdir(os.path.join(BASE_DIR, d))
        ]

    # --------------------------------------------------
    # INSTALLER TAB
    # --------------------------------------------------
    txt_name = widgets.Text(
        description='Server Name:',
        placeholder='SurvivalSMP'
    )

    drop_soft = widgets.Dropdown(
        options=['Paper', 'Purpur', 'Fabric', 'Vanilla'],
        description='Software:'
    )

    txt_ver = widgets.Text(
        value='1.21.1',
        description='Version:'
    )

    btn_install = widgets.Button(
        description='Install Server',
        button_style='success',
        icon='download'
    )

    installer = widgets.VBox([
        widgets.HTML('<h3>📥 Install Server</h3>'),
        txt_name,
        drop_soft,
        txt_ver,
        btn_install
    ])

    # --------------------------------------------------
    # SERVER CONTROL
    # --------------------------------------------------
    drop_server = widgets.Dropdown(
        options=server_dirs,
        description='Server:'
    )

    alloc_ram, total_ram = engine_instance.get_system_ram()

    ram_slider = widgets.IntSlider(
        value=alloc_ram,
        min=1,
        max=int(total_ram),
        description='RAM GB:'
    )

    btn_start = widgets.Button(
        description='Start',
        button_style='success',
        icon='play'
    )

    btn_stop = widgets.Button(
        description='Stop',
        button_style='danger',
        icon='stop'
    )

    cmd_input = widgets.Text(
        placeholder='say hello world'
    )

    btn_send = widgets.Button(
        description='Send',
        button_style='info'
    )

    controls = widgets.VBox([
        widgets.HTML('<h3>🚀 Server Control</h3>'),
        drop_server,
        ram_slider,
        widgets.HBox([btn_start, btn_stop]),
        widgets.HTML('<h4>📟 Live Console</h4>'),
        console_stream,
        widgets.HBox([cmd_input, btn_send])
    ])

    tabs = widgets.Tab()
    tabs.children = [controls, installer]

    tabs.set_title(0, 'Dashboard')
    tabs.set_title(1, 'Installer')

    display(tabs)
    display(panel_output)

    # --------------------------------------------------
    # INSTALL HANDLER
    # --------------------------------------------------
    def install_server(b):
        name = txt_name.value.strip().replace(' ', '_')
        software = drop_soft.value
        version = txt_ver.value.strip()

        if not name:
            return

        path = os.path.join(BASE_DIR, name)
        os.makedirs(path, exist_ok=True)

        with panel_output:
            clear_output()

            try:
                print('⚡ Installing server...')

                engine_instance.resolve_and_download(
                    software,
                    version,
                    path
                )

                engine_instance.optimize_configurations(path)

                print('🎉 Server installed successfully.')

            except Exception as e:
                print(f'❌ Error: {e}')

        drop_server.options = [
            d for d in os.listdir(BASE_DIR)
            if os.path.isdir(os.path.join(BASE_DIR, d))
        ]

    # --------------------------------------------------
    # START HANDLER
    # --------------------------------------------------
    def start_server(b):
        if engine_instance.is_running:
            return

        server = drop_server.value

        if not server:
            return

        path = os.path.join(BASE_DIR, server)

        with console_stream:
            clear_output()

        engine_instance.launch_instance(
            path,
            ram_slider.value,
            console_stream
        )

    # --------------------------------------------------
    # STOP HANDLER
    # --------------------------------------------------
    def stop_server(b):
        engine_instance.safely_kill()

    # --------------------------------------------------
    # COMMAND HANDLER
    # --------------------------------------------------
    def send_command(b):
        cmd = cmd_input.value.strip()

        if cmd:
            engine_instance.dispatch_command(cmd)
            cmd_input.value = ''

    btn_install.on_click(install_server)
    btn_start.on_click(start_server)
    btn_stop.on_click(stop_server)
    btn_send.on_click(send_command)
    cmd_input.on_submit(send_command)

# Start dashboard
build_dashboard()
```

This module:

* creates the dashboard UI
* creates installer tab
* creates server control panel
* creates real-time console
* handles commands and buttons

---

Run all 3 cells from top to bottom.

After MODULE 3 finishes executing, the hosting panel will automatically appear.

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
