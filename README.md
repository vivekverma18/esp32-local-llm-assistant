# SenseCAP Watcher Local AI Assistant - Complete Tutorial

Build your own 100% private AI assistant with vision and voice capabilities using ESP32 and local LLM - No cloud required!

![Platform](https://img.shields.io/badge/platform-ESP32-green.svg)
![Language](https://img.shields.io/badge/language-Python-yellow.svg)

## 📋 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Hardware Requirements](#hardware-requirements)
- [Software Requirements](#software-requirements)
- [Complete Setup Tutorial](#complete-setup-tutorial)
- [Configuration](#configuration)
- [Usage Examples](#usage-examples)
- [Troubleshooting](#troubleshooting)
- [Performance Metrics](#performance-metrics)
- [Contributing](#contributing)
- [Resources](#resources)
- [License](#license)

## 🎯 Overview

This project transforms a SenseCAP Watcher into a completely private AI assistant that processes everything locally on your network. Unlike cloud-based assistants (Alexa, Google Home), your data never leaves your premises. The system uses edge computing with an ESP32-S3 microcontroller and runs a Qwen/DeepSeek LLM on your local machine.

### Why Local AI?
- **Complete Privacy**: Data never leaves your network
- **No Subscription Fees**: One-time hardware cost only
- **Low Latency**: No round-trip to cloud servers
- **Offline Operation**: Works without internet
- **Full Control**: Customize every aspect
- **Data Sovereignty**: You own your data

## ✨ Features

- 🎤 **Voice Commands**: Natural language interaction with wake-word detection
- 📷 **Vision Processing**: Camera-based object recognition and scene understanding
- 🧠 **Multimodal AI**: Qwen LLM processes both text and images
- 🔒 **100% Private**: All processing on local network
- ⚡ **Real-time Response**: 2-4 second total response time
- 🏠 **Smart Home Integration**: Control devices via MCP protocol
- 🔌 **Low Power**: ~2W idle, ~5W active consumption
- 📱 **Mobile Config**: Easy setup via SenseCraft app



## 🏗️ Architecture

<p align="center">
  <img src="https://github.com/vivekverma18/esp32-local-llm-assistant/blob/main/Imported%20from%20excalidraw.com.excalidraw.png" width="800" alt="Local AI Assistant Architecture">
</p>



### Data Flow Pipeline
1. **Wake Word Detection** → Himax AI chip (on-device)
2. **Audio/Video Capture** → ESP32-S3 processing
3. **Data Encryption** → Local network transmission
4. **AI Processing** → Ollama server with Qwen LLM
5. **Response Generation** → Text-to-speech conversion
6. **Audio Playback** → SenseCAP Watcher speaker

## 🔧 Hardware Requirements

### Essential Components

| Component | Specification | Price (USD) | Purchase Link |
|-----------|--------------|-------------|---------------|
| **SenseCAP Watcher** | ESP32-S3 + Himax AI | $89 | [Seeed Studio](https://www.seeedstudio.com/sensecap-watcher) |
| **Local Server** | Min 8GB RAM, GPU recommended | Existing PC | - |
| **USB-C Cable** | For initial flashing | $5 | Any electronics store |
| **WiFi Router** | 2.4GHz support required | Existing | - |

### SenseCAP Watcher Specifications
- **MCU**: ESP32-S3 (Dual-core Xtensa LX7 @ 240MHz)
- **AI Processor**: Himax WiseEye2 HX6538
- **Memory**: 8MB PSRAM, 16MB Flash
- **Camera**: 2MP OV2640
- **Microphone**: High-sensitivity MEMS
- **Speaker**: Built-in 1W
- **Connectivity**: Wi-Fi 802.11 b/g/n
- **Power**: 5V via USB-C

### Server Requirements

#### Minimum Configuration
- CPU: 4 cores, 2.5GHz+
- RAM: 8GB
- Storage: 20GB free space
- OS: Windows 10/11, Ubuntu 20.04+, macOS 12+

**Framework Desktop (My Actual Build):**
- **CPU**: AMD Ryzen™ 9 Maxx 395 
- **RAM**: 128GB DDR5 
- **Storage**: 1TB WD BLACK™ SN760 NVMe™ M.2 2280
- **Cooling**: Cooler Master Mobius 120
- **GPU**: Integrated AMD Radeon graphics (discrete GPU can be added)
- **Why This Config**: Massive 128GB RAM for loading multiple LLMs, desktop Ryzen 9 for sustained high performance without thermal throttling

## 💻 Software Requirements

### Required Software
- **Python**: 3.8 or higher
- **Git**: For version control
- **Ollama**: LLM runtime environment
- **esptool**: For flashing ESP32

### Models & Firmware
- **LLM Model**: Qwen-VL or DeepSeek-VL (multimodal)
- **Firmware**: XiaoZhi ESP32 Conversational Agent
- **Mobile App**: SenseCraft (iOS/Android)

## 📖 Complete Setup Tutorial

### Phase 1: Prepare Your Computer

#### Step 1.1: Install Python
```bash
# Windows: Download from python.org
# macOS: brew install python3
# Linux: sudo apt-get install python3 python3-pip
```

#### Step 1.2: Install Git
```bash
# Windows: Download from git-scm.com
# macOS: brew install git
# Linux: sudo apt-get install git
```

#### Step 1.3: Install ESP Tools
```bash
pip install esptool
pip install pyserial
```

### Phase 2: Setup Ollama and LLM

#### Step 2.1: Install Ollama

**Windows:**
1. Download installer from https://ollama.com/download/windows
2. Run OllamaSetup.exe
3. Follow installation wizard

**macOS/Linux:**
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

#### Step 2.2: Download Qwen Model
```bash
# Start Ollama service
ollama serve

# In new terminal, pull the model (this takes 10-30 minutes)
ollama pull qwen:latest

# Verify installation
ollama list
```

#### Step 2.3: Configure Ollama for Network Access
```bash
# Linux/macOS: Edit service file
sudo systemctl edit ollama.service

# Add these lines:
[Service]
Environment="OLLAMA_HOST=0.0.0.0"

# Windows: Set environment variable
setx OLLAMA_HOST "0.0.0.0"

# Restart Ollama
sudo systemctl restart ollama
```

### Phase 3: Flash SenseCAP Watcher

#### Step 3.1: Download XiaoZhi Firmware
```bash
# Create project directory
mkdir sensecap-ai && cd sensecap-ai

# Download firmware
wget https://github.com/78/xiaozhi-esp32/releases/latest/download/xiaozhi-watcher.bin

# Alternative: Clone entire repository
git clone https://github.com/78/xiaozhi-esp32.git
```

#### Step 3.2: Prepare Device for Flashing
1. Connect SenseCAP Watcher to computer via USB-C
2. Hold the BOOT button while pressing RESET
3. Release RESET, then release BOOT
4. Device is now in download mode

#### Step 3.3: Find Device Port
```bash
# Windows
# Open Device Manager, look under "Ports (COM & LPT)"
# Note the COM port (e.g., COM3)

# Linux
ls /dev/ttyUSB*
# Usually /dev/ttyUSB0

# macOS
ls /dev/tty.*
# Look for /dev/tty.usbserial-*
```

#### Step 3.4: Flash the Firmware
```bash
# Replace PORT with your actual port
esptool.py --chip esp32s3 --port PORT --baud 460800 \
  --before default_reset --after hard_reset \
  write_flash -z --flash_mode dio --flash_freq 80m \
  0x0 xiaozhi-watcher.bin

# Example for Windows:
esptool.py --chip esp32s3 --port COM3 --baud 460800 write_flash 0x0 xiaozhi-watcher.bin

# Example for Linux:
esptool.py --chip esp32s3 --port /dev/ttyUSB0 --baud 460800 write_flash 0x0 xiaozhi-watcher.bin
```

#### Step 3.5: Verify Flashing
After successful flashing, you should see:
```
Leaving...
Hard resetting via RTS pin...
```

### Phase 4: Configure Device

#### Step 4.1: Install SenseCraft App
- **iOS**: Search "SenseCraft" in App Store
- **Android**: Search "SenseCraft" in Google Play Store

#### Step 4.2: Find Your Computer's IP Address
```bash
# Windows
ipconfig
# Look for "IPv4 Address"

# Linux/macOS
ifconfig
# or
ip addr show
# Look for inet address on your network interface
```

#### Step 4.3: Configure via SenseCraft App
1. Open SenseCraft app
2. Click "+" to add new device
3. Select "SenseCAP Watcher"
4. Follow WiFi setup wizard:
   - Enter your WiFi SSID
   - Enter WiFi password
   - Device will connect to network
5. Configure AI Server:
   - Server Type: Custom
   - Server URL: `http://YOUR_COMPUTER_IP:11434`
   - Example: `http://192.168.1.100:11434`
6. Test connection

### Phase 5: Test the System

#### Step 5.1: Basic Voice Test
1. Say the wake word: "Hi Watcher" or "Hey Watcher"
2. Wait for acknowledgment tone
3. Ask: "What's the weather like?"
4. System should respond (even if it doesn't have weather data)

#### Step 5.2: Vision Test
1. Place an object in front of the Watcher
2. Say: "Hey Watcher, what do you see?"
3. The AI should describe what's visible to the camera

#### Step 5.3: Complex Interaction Test
1. "Hey Watcher, what's on my desk?"
2. "Can you count the items?"
3. "What color is the largest object?"

## ⚙️ Configuration

### WiFi Configuration
Edit device settings in SenseCraft app:
- Network: 2.4GHz only (5GHz not supported)
- Security: WPA2 recommended
- Static IP: Optional but recommended for server

### Ollama Server Configuration

Create `ollama-config.yaml`:
```yaml
server:
  host: 0.0.0.0
  port: 11434
  cors_allowed_origins:
    - "*"
  
models:
  default: qwen:latest
  timeout: 30
  max_tokens: 1024
  temperature: 0.7

performance:
  num_threads: 8
  gpu_layers: 35  # Adjust based on GPU VRAM
  context_size: 2048
```

### Wake Word Customization

Modify in XiaoZhi firmware config:
```json
{
  "wake_words": [
    "hey_watcher",
    "ok_watcher",
    "hi_assistant"
  ],
  "sensitivity": 0.6,
  "timeout": 10
}
```

## 💡 Usage Examples

### Basic Commands
- "What time is it?"
- "Tell me a joke"
- "Explain quantum computing"

### Vision Commands
- "Describe what you see"
- "How many people are in the room?"
- "What color is my shirt?"
- "Read the text on this page"

### Complex Interactions
- "Look at this circuit board and identify the components"
- "Watch me perform this gesture and describe it"
- "Is my posture correct for this exercise?"

### Smart Home Integration (with additional setup)
- "Turn on the living room lights"
- "Set temperature to 22 degrees"
- "Is the garage door open?"

## 🔧 Troubleshooting

### Issue: Watcher Doesn't Respond to Wake Word

**Solutions:**
1. Check microphone sensitivity in config
2. Reduce background noise
3. Speak clearly and directly to device
4. Verify firmware flashed correctly

### Issue: "Connection Failed" in SenseCraft App

**Solutions:**
1. Verify Ollama is running:
```bash
curl http://localhost:11434/api/tags
```
2. Check firewall settings:
```bash
# Windows: Allow through firewall
netsh advfirewall firewall add rule name="Ollama" dir=in action=allow protocol=TCP localport=11434

# Linux: Open port
sudo ufw allow 11434
```
3. Ensure both devices on same network
4. Try using computer's hostname instead of IP

### Issue: Slow Response Times

**Solutions:**
1. Use GPU acceleration:
```bash
# Check GPU usage
nvidia-smi

# Configure Ollama for GPU
OLLAMA_GPU_LAYERS=35 ollama serve
```
2. Reduce model size:
```bash
ollama pull qwen:7b  # Instead of larger model
```
3. Optimize context window in config
4. Check network latency

### Issue: Poor Vision Recognition

**Solutions:**
1. Improve lighting conditions
2. Clean camera lens
3. Position objects 30-100cm from camera
4. Update to latest model version

### Issue: Firmware Flashing Fails

**Solutions:**
1. Try lower baud rate:
```bash
esptool.py --chip esp32s3 --port COM3 --baud 115200 write_flash 0x0 xiaozhi-watcher.bin
```
2. Hold BOOT button during entire flash process
3. Use shorter USB cable
4. Try different USB port

## 🚀 Advanced Configuration

### Running Multiple Models

```bash
# Download additional models
ollama pull llava:latest  # Better vision
ollama pull mistral:latest  # Faster inference

# Switch models via API
curl http://localhost:11434/api/generate -d '{
  "model": "llava:latest",
  "prompt": "What do you see?"
}'
```

### Custom Wake Words

1. Train custom wake word model
2. Convert to TensorFlow Lite
3. Flash to Himax chip
4. Update configuration

### API Integration

Create `server.py`:
```python
import requests
import json

class LocalAIServer:
    def __init__(self, host="localhost", port=11434):
        self.base_url = f"http://{host}:{port}"
    
    def query(self, prompt, model="qwen:latest"):
        response = requests.post(
            f"{self.base_url}/api/generate",
            json={"model": model, "prompt": prompt}
        )
        return response.json()

# Usage
ai = LocalAIServer()
result = ai.query("What is the meaning of life?")
print(result['response'])
```

## 🤝 Contributing

Contributions are welcome! Areas for improvement:

- [ ] Add more language support
- [ ] Improve wake word detection
- [ ] Create web interface
- [ ] Add home automation integrations
- [ ] Optimize for lower-spec hardware
- [ ] Create custom skills/plugins
- [ ] Improve documentation

### How to Contribute
1. Fork the repository
2. Create feature branch
3. Commit changes
4. Push to branch
5. Open Pull Request

## 📚 Resources

### Official Documentation
- [SenseCAP Watcher Wiki](https://wiki.seeedstudio.com/flash_watcher_agent_firmware/)
- [XiaoZhi Firmware (Chinese)](https://ccnphfhqs21z.feishu.cn/wiki/F5krwD16viZoF0kKkvDcrZNYnhb)
- [XiaoZhi GitHub (English)](https://github.com/78/xiaozhi-esp32/blob/main/README_en.md)
- [Ollama Documentation](https://ollama.com/docs)

### Community Resources
- [ESP32 Forum](https://esp32.com)
- [Ollama Discord](https://discord.gg/ollama)
- [Seeed Studio Forum](https://forum.seeedstudio.com)

### Video Tutorials
- Coming soon!

### Related Projects
- [ESP32-ChatGPT](https://github.com/example/esp32-chatgpt)
- [Local-First-AI](https://github.com/example/local-first-ai)
- [Privacy-Assistant](https://github.com/example/privacy-assistant)

## 📈 Project Roadmap

### Version 0.1.0 (Current)
- ✅ Basic voice interaction
- ✅ Vision processing
- ✅ Local LLM integration

### Version 0.2.0 (Planned)
- ⬜ Web dashboard
- ⬜ Multi-device support
- ⬜ Voice customization

### Version 0.3.0 (Future)
- ⬜ Home Assistant integration
- ⬜ Custom skill framework
- ⬜ Mobile app for management

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ⭐ Acknowledgments

- **Seeed Studio**: For the amazing SenseCAP Watcher hardware
- **XiaoZhi Team**: For the open-source ESP32 firmware
- **Ollama Community**: For making local LLMs accessible
- **Qwen/DeepSeek Teams**: For the multimodal models
- **Contributors**: Everyone who has helped improve this project
