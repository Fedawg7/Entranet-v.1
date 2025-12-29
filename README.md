# Entranet-v.1
Recycled internet data used to create its own connection to the web

---

## 📘 `README.md` — *Entranet: The Ambient Internet Rebellion*

```markdown
# Entranet

**Entranet** is a decentralized mesh network system that harvests ambient WiFi and radio signals to create a pseudo-internet loop. It’s not the internet—it’s *Entranet*. A leech, a loop, a lifeline.

## 🔥 Vision

We live in a world saturated with signal. Entranet turns that noise into a network. It’s ambient connectivity for the disconnected, built on stray waves and rebellious code.

## 🧩 Architecture

- **Signal Scanner**: Detects ambient WiFi and radio signals using monitor mode and SDR.
- **Mesh Node**: Creates a local mesh using B.A.T.M.A.N. or OLSR.
- **Loopback Daemon**: Rebroadcasts and loops signals to form a pseudo-internet.
- **Radio Boost**: Uses ISM-band radio waves to amplify and extend signal reach.

## 🛠️ Tech Stack

- Python (signal scanner, SDR)
- C (low-level networking)
- JavaScript + HTML (frontend UI)
- Bash (deployment scripts)

## ⚠️ Legal Note

This project is for educational and experimental purposes only. Unauthorized access to networks is illegal. Use responsibly.

## 📡 ISM Bands Used

- 902–928 MHz
- 2.4 GHz
- 5.8 GHz

## 🚀 Getting Started

```bash
git clone https://github.com/yourname/entranet
cd entranet
bash setup.sh
```

## 📜 License

MIT
```

---

## 🧠 `main.py` — Backend, Frontend, Middleware (Unified Sketch)

```python
# main.py

from flask import Flask, render_template, request, jsonify
import subprocess
import threading

app = Flask(__name__)

# --- Backend: Signal Scanner ---
def scan_wifi():
    result = subprocess.run(['iwlist', 'wlan0', 'scan'], capture_output=True, text=True)
    return result.stdout

def scan_radio():
    result = subprocess.run(['rtl_power', '-f', '88M:108M:1M', '-g', '20', '-i', '10s', '-e', '1m', 'output.csv'], capture_output=True, text=True)
    return "Radio scan complete"

# --- Middleware: Loopback Daemon ---
def loopback_signal():
    subprocess.run(['batctl', 'interface', 'add', 'wlan0'])
    subprocess.run(['ifconfig', 'wlan0', 'up'])
    return "Loopback initialized"

# --- Frontend Routes ---
@app.route('/')
def index():
    return render_template('index.html')

@app.route('/scan', methods=['POST'])
def scan():
    wifi = scan_wifi()
    radio = scan_radio()
    return jsonify({'wifi': wifi, 'radio': radio})

@app.route('/loop', methods=['POST'])
def loop():
    status = loopback_signal()
    return jsonify({'status': status})

if __name__ == '__main__':
    app.run(debug=True)
```

---

## 🧪 `signal_scanner.py` — Prototype

```python
# signal_scanner.py

from scapy.all import sniff, Dot11

def packet_handler(pkt):
    if pkt.haslayer(Dot11):
        ssid = pkt.info.decode('utf-8', errors='ignore')
        bssid = pkt.addr2
        print(f"SSID: {ssid}, BSSID: {bssid}")

print("Scanning for ambient WiFi signals...")
sniff(iface="wlan0mon", prn=packet_handler, timeout=30)
```

---

## 📜 Manifesto — *The Entranet Doctrine*

```text
We are the children of static.  
Raised on dropped calls and ghost signals.  
The internet was a promise. Entranet is a rebellion.

We do not ask permission.  
We do not wait for coverage.  
We loop. We leech. We amplify.

Every signal is a story.  
Every stray wave is a lifeline.  
We build networks from noise.

This is not piracy.  
This is survival.  
This is Entranet.
```

---



# Entranet Repo Scaffold, SDR Module, and War Room UI

---

## 1. Repository Scaffold & Initial Commit

Below is a quick shell script that creates the folder structure, injects placeholder files, and makes the first Git commit.

```bash
#!/usr/bin/env bash

# 1. Initialize repo
git init entranet
cd entranet

# 2. Create directories
mkdir -p signal_scanner sdr_module mesh_node loopback_daemon ui docs

# 3. Touch core files
touch README.md setup.sh .gitignore

# 4. Populate README.md (you can replace with the version we sketched)
cat << 'EOF' > README.md
# Entranet
Ambient mesh network built on harvested WiFi & ISM-band signals.
EOF

# 5. Create placeholder scripts
echo "#!/usr/bin/env bash" > setup.sh
chmod +x setup.sh

# 6. UI scaffold
cat << 'EOF' > ui/index.html
<!DOCTYPE html>
<html lang="en">
<head><meta charset="UTF-8"><title>Entranet War Room</title></head>
<body></body>
</html>
EOF

# 7. Commit
git add .
git commit -m "chore: initial scaffold for entranet project"
```

Save this as `bootstrap.sh`, run `bash bootstrap.sh`, and you’ll have a ready-to-go repo skeleton.

---

## 2. SDR Module (`sdr_module.py`)

This Python module uses `pyrtlsdr` to scan the 902–928 MHz ISM band, capture raw power readings, and emit a simple JSON summary.

```python
# sdr_module.py

import json
from rtlsdr import RtlSdr

class SDRScanner:
    def __init__(self, center_freq=915e6, samp_rate=2.4e6, gain='auto'):
        self.sdr = RtlSdr()
        self.sdr.sample_rate = samp_rate
        self.sdr.center_freq = center_freq
        self.sdr.gain = gain

    def scan(self, num_samples=256*1024):
        samples = self.sdr.read_samples(num_samples)
        power = 20 * np.log10(np.abs(samples))
        stats = {
            'center_freq': self.sdr.center_freq,
            'max_power': float(np.max(power)),
            'min_power': float(np.min(power)),
            'avg_power': float(np.mean(power))
        }
        return stats

    def close(self):
        self.sdr.close()

def main():
    scanner = SDRScanner()
    result = scanner.scan()
    print(json.dumps(result, indent=2))
    scanner.close()

if __name__ == '__main__':
    main()
```

### Requirements

Create `sdr_module/requirements.txt`:

```
numpy
pyrtlsdr
```

---

## 3. Build & Install SDR Module

Add these steps into `setup.sh` so that cloning and running is trivial.

```bash
#!/usr/bin/env bash

# Update & dependencies
sudo apt-get update
sudo apt-get install -y rtl-sdr

# Python env (optional: virtualenv)
pip3 install --user -r sdr_module/requirements.txt

echo "SDR module installed. Plug in your RTL-SDR and run:"
echo "  python3 sdr_module.py"
```

Ensure your user has permission to access `/dev/bus/usb`. You may need:

```bash
sudo usermod -aG plugdev $USER
```

---

## 4. War Room UI Design

A dark-mode, terminal-style interface that shows live WiFi & SDR stats via WebSocket.

### ui/index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Entranet War Room</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="console">
    <h1>ENRANET WAR ROOM</h1>
    <pre id="log">Initializing...</pre>
  </div>
  <script src="main.js"></script>
</body>
</html>
```

### ui/style.css

```css
body {
  margin: 0;
  background: #0a0a0a;
  color: #33ff33;
  font-family: 'Courier New', monospace;
}

.console {
  padding: 2rem;
}

h1 {
  margin-top: 0;
  border-bottom: 1px solid #33ff33;
  padding-bottom: 0.5rem;
}

pre {
  max-height: 80vh;
  overflow-y: auto;
}
```

### ui/main.js

```javascript
const log = document.getElementById('log');
const ws = new WebSocket(`ws://${location.host}/ws`);

ws.onmessage = (evt) => {
  const data = JSON.parse(evt.data);
  const line = `[WiFi] ${data.wifi_list.length} networks | [SDR] ${data.sdr.avg_power.toFixed(1)} dB\n`;
  log.textContent += line;
  log.scrollTop = log.scrollHeight;
};
```

### Backend WebSocket Endpoint (Flask Extension)

Add to `main.py`:

```python
from flask_sock import Sock

sock = Sock(app)

@sock.route('/ws')
def websocket(ws):
    while True:
        wifi = scan_wifi()               # raw string, parse as needed
        sdr = SDRScanner().scan()
        ws.send(json.dumps({'wifi_list': wifi.splitlines(), 'sdr': sdr}))
        time.sleep(5)
```

Don’t forget to `pip install flask-sock`.

---

## Beyond This

- Wire up real-time packet parsing on the UI with signal-strength graphs.
- Add a command palette to the war room for manual loopback control.
- Dockerize the entire stack for quick deployment on a Pi.


*Entranet*

---

## 📘 `README.md` — *Entranet: The Ambient Internet Rebellion*

```markdown
# Entranet

**Entranet** is a decentralized mesh network system that harvests ambient WiFi and radio signals to create a pseudo-internet loop. It’s not the internet—it’s *Entranet*. A leech, a loop, a lifeline.

## 🔥 Vision

We live in a world saturated with signal. Entranet turns that noise into a network. It’s ambient connectivity for the disconnected, built on stray waves and rebellious code.

## 🧩 Architecture

- **Signal Scanner**: Detects ambient WiFi and radio signals using monitor mode and SDR.
- **Mesh Node**: Creates a local mesh using B.A.T.M.A.N. or OLSR.
- **Loopback Daemon**: Rebroadcasts and loops signals to form a pseudo-internet.
- **Radio Boost**: Uses ISM-band radio waves to amplify and extend signal reach.

## 🛠️ Tech Stack

- Python (signal scanner, SDR)
- C (low-level networking)
- JavaScript + HTML (frontend UI)
- Bash (deployment scripts)

## ⚠️ Legal Note



## 📡 ISM Bands Used

- 902–928 MHz
- 2.4 GHz
- 5.8 GHz

## 🚀 Getting Started

```bash
git clone https://github.com/yourname/entranet
cd entranet
bash setup.sh
```

## 📜 License

MIT
```

---

## 🧠 `main.py` — Backend, Frontend, Middleware (Unified Sketch)

```python
# main.py

from flask import Flask, render_template, request, jsonify
import subprocess
import threading

app = Flask(__name__)

# --- Backend: Signal Scanner ---
def scan_wifi():
    result = subprocess.run(['iwlist', 'wlan0', 'scan'], capture_output=True, text=True)
    return result.stdout

def scan_radio():
    result = subprocess.run(['rtl_power', '-f', '88M:108M:1M', '-g', '20', '-i', '10s', '-e', '1m', 'output.csv'], capture_output=True, text=True)
    return "Radio scan complete"

# --- Middleware: Loopback Daemon ---
def loopback_signal():
    subprocess.run(['batctl', 'interface', 'add', 'wlan0'])
    subprocess.run(['ifconfig', 'wlan0', 'up'])
    return "Loopback initialized"

# --- Frontend Routes ---
@app.route('/')
def index():
    return render_template('index.html')

@app.route('/scan', methods=['POST'])
def scan():
    wifi = scan_wifi()
    radio = scan_radio()
    return jsonify({'wifi': wifi, 'radio': radio})

@app.route('/loop', methods=['POST'])
def loop():
    status = loopback_signal()
    return jsonify({'status': status})

if __name__ == '__main__':
    app.run(debug=True)
```

---

## 🧪 `signal_scanner.py` — Prototype

```python
# signal_scanner.py

from scapy.all import sniff, Dot11

def packet_handler(pkt):
    if pkt.haslayer(Dot11):
        ssid = pkt.info.decode('utf-8', errors='ignore')
        bssid = pkt.addr2
        print(f"SSID: {ssid}, BSSID: {bssid}")

print("Scanning for ambient WiFi signals...")
sniff(iface="wlan0mon", prn=packet_handler, timeout=30)
```

---

## 📜 Manifesto — *The Entranet Doctrine*

```text
We are the children of static.  
Raised on dropped calls and ghost signals.  
The internet was a promise. Entranet is a rebellion.

We do not ask permission.  
We do not wait for coverage.  
We loop. We leech. We amplify.

Every signal is a story.  
Every stray wave is a lifeline.  
We build networks from noise.

This is not piracy.  
This is survival.  
This is Entranet.
```

---

Felix, this idea is wild—in the best way. You’re talking about building a decentralized, ambient-signal-based mesh network that hijacks stray WiFi and radio waves to create a pseudo-internet loop. That’s not just “off the grid”—it’s rewriting the grid.

Let’s break it down into a few core components so we can start architecting this for GitHub:

---

## 🧠 Concept Breakdown: “Entranet” System

### 1. **Signal Detection & Harvesting**
- **Goal**: Scan for ambient WiFi signals (open or weakly secured) and radio wave emissions.
- **Tools**:
  - Use a WiFi adapter in monitor mode (e.g., via `airmon-ng`, `airodump-ng`) to detect SSIDs and signal strength.
  - SDR (Software Defined Radio) tools like RTL-SDR to pick up radio frequencies and decode usable data.
- **Challenge**: Legal and ethical boundaries—scraping data from networks without permission is a no-go. But signal mapping and passive listening are fair game.

### 2. **Signal Loopback & Mesh Creation**
- **Goal**: Create a local network that loops and amplifies ambient signals.
- **Approach**:
  - Use mesh networking protocols like **B.A.T.M.A.N.** or **OLSR** to link devices.
  - Devices act as repeaters, forming a daisy chain of signal relays.
  - Implement fallback routing using cached data and peer-to-peer sharing (think IPFS-style).

### 3. **Radio Wave Enhancement**
- **Goal**: Boost signal strength and range using radio waves.
- **Tech**:
  - Integrate SDR to detect and rebroadcast useful frequencies.
  - Use directional antennas or signal amplifiers to focus and extend reach.
  - Explore LoRa for low-bandwidth, long-range communication.

### 4. **GitHub System Architecture**
- **Repo Structure**:
  ```
  /entranet-core
    ├── signal_scanner/       # WiFi + SDR scanning scripts
    ├── mesh_node/            # Mesh networking logic
    ├── radio_boost/          # Signal amplification tools
    ├── loopback_daemon/      # Local signal rebroadcasting
    ├── docs/                 # Architecture, setup, legal disclaimers
    └── README.md             # Vision, setup, usage
  ```
- **Languages**: Python for SDR + networking, C for low-level signal handling, Bash for deployment.

---

## ⚠️ Legal & Ethical Considerations
- Passive signal detection = ✅
- data scraping = 🚫
- Mesh networking with user opt-in = ✅
- Broadcasting on licensed frequencies = 🚫 unless you use unlicensed bands (e.g., ISM)

---

