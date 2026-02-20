# OpenClaw Easy Deploy

This repository incredibly simplifies the deployment, installation, and strict security-hardening of your personal instance of [OpenClaw](https://openclaw.ai) onto a fresh VPS (Virtual Private Server).

## 🚀 Operating System Requirements

**This script is strictly designed for Debian-based Linux distributions.** 

**✅ Recommended Supported Systems:**
- Ubuntu 24.04 LTS *(Highly Recommended)*
- Ubuntu 22.04 LTS
- Debian 13
- Debian 12

Because this script acts as an automated SysAdmin, it explicitly relies on the `apt` package manager, `ufw` firewall, and Debian-specific configurations (like `unattended-upgrades`). It will **not** run on Red Hat, CentOS, Fedora, or Arch Linux.

## 🔒 Security Built-In

Instead of just installing OpenClaw, these scripts act as a full SysAdmin for you. They automatically:
1. **Create a `2GB` Swapfile** to prevent the Linux kernel from killing Node.js on cheap VPS servers.
2. **Create a non-root user (`openclaw`)** and install OpenClaw under that user.
3. **Change the SSH Port to `2222`** blocking 99% of global automated botnet scanners.
4. **Install `fail2ban`** to deter and instantly block continuous attack attempts.
5. **Lock down the UFW firewall**, permanently closing all ports (`80`, `443`, `18789`) leaving only your secret SSH port.
6. **Setup automatic background security patches (`unattended-upgrades`)**.
7. **Disable your `root` user SSH access completely.**

---

## 🛠️ One-Command Installation

Log into your fresh VPS as `root`. Run the following command:

```bash
bash <(curl -s https://raw.githubusercontent.com/stfurkan/claw-easy-setup/main/setup-server.sh)
```
> **🧠 Intelligent Setup:**
> *This script will automatically detect if you have SSH Keys installed on the server. If you do, it will copy them to your new user and provide a one-liner to disable password login after you verify key access works. If you don't have keys, it will let you create a strong password instead!*

### ⚙️ Advanced Customization (Optional)
By default, the script creates a user named `openclaw` and moves SSH to port `2222`. You can customize this by passing flags to the script:
- `-u` : Custom username
- `-p` : Custom SSH port

```bash
# Example 1: Change only the SSH Port
bash <(curl -s https://raw.githubusercontent.com/stfurkan/claw-easy-setup/main/setup-server.sh) -p 8888

# Example 2: Change the username and the SSH Port
bash <(curl -s https://raw.githubusercontent.com/stfurkan/claw-easy-setup/main/setup-server.sh) -u myadmin -p 8888
```

Grab a coffee! By the time the script finishes, your server will be heavily fortified, and the OpenClaw environment will be ready.

---

## 🚇 CLI Setup & Accessing the Control UI

After the server provisioning finishes, your raw OpenClaw environment is ready. Now you must run the interactive CLI setup to configure your API keys and daemon!

**1. Log into your fortified server:**
*(Remember, your port is now `2222`, and your user is `openclaw`!)*
```bash
ssh -p 2222 openclaw@<YOUR_SERVER_IP>
```

**2. Run the internal Setup Wizard:**
```bash
openclaw onboard --install-daemon
```
*Follow the interactive prompts in your terminal to provide API keys and configure the assistant.*

**3. (If SSH Keys detected) Lock down password login:**
After confirming you can log in with your SSH key, run this on the server to disable password authentication:
```bash
sudo sed -i 's/^PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config.d/00-openclaw-security.conf && sudo sshd -t && sudo systemctl restart sshd
```
> ⚠️ **Only run this after verifying key-based login works!** If you skip this step, your server will still be protected by Fail2Ban, but key-only login is the gold standard.

**4. Accessing the Dashboard Setup (Optional but recommended):**
If you ever want to access the visual OpenClaw Dashboard securely without exposing port 18789 to the internet, run this **SSH Tunnel** command from your **Local Computer** terminal (not the server):
```bash
ssh -p 2222 -L 18789:localhost:18789 openclaw@<YOUR_SERVER_IP>
```
*Leave that terminal window open to keep the tunnel active. Open your browser and go to your dashboard:*
[http://localhost:18789](http://localhost:18789)

### Useful Maintenance Commands
In the future, when you SSH into your server, do so like this:
`ssh -p 2222 openclaw@<YOUR_SERVER_IP>`

Once inside, use the OpenClaw CLI like normal:
`openclaw gateway status`

---

## ⚠️ Disclaimer

This project is provided **"as-is"** for **educational and informational purposes only**. The authors and contributors take **no responsibility** for any damage, data loss, server lockouts, security breaches, or any other issues that may arise from using these scripts.

**By using this software, you acknowledge that:**
- You are solely responsible for your server and its security.
- You should always **test on a disposable server** before deploying to production.
- You should **maintain backups** and have a recovery plan (e.g., VPS console access) before running any automation scripts.
- No script can guarantee 100% security. Always follow your own due diligence.

This project is not affiliated with or endorsed by OpenClaw. Use at your own risk.
