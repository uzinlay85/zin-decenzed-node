# 🛡️ Ubuntu VPS အသစ်များအတွက် စတင်လုပ်ဆောင်ရမည့် Security Baseline နှင့် ပြဿနာဖြေရှင်းနည်း လမ်းညွှန်အပြည့်အစုံ

ဤလမ်းညွှန်သည် Ubuntu 22.04 / 24.04 LTS အသစ်ဝယ်ယူထားသော VPS Server များတွင် **Network/APT Error များ ကင်းဝေးစေပြီး** လုံခြုံရေးအဆင့်အတန်းမြင့်မားသော **Security Baseline** နှင့် **Proxy Node အဆင်သင့်ဖြစ်စေမည့် စနစ်** ကို တစ်ကြိမ်တည်းဖြင့် အပြည့်အစုံ တည်ဆောက်နိုင်ရန် ရေးသားထားပါသည်။

---

## 📌 မာတိကာ (Table of Contents)
1. [ကြုံတွေ့ရတတ်သော ပြဿနာများနှင့် ဖြေရှင်းချက်များ (Learned Solutions)](#၁-ကြုံတွေ့ရတတ်သော-ပြဿနာများနှင့်-ဖြေရှင်းချက်များ)
2. [အပြည့်စုံဆုံး Automated Security Baseline Script](#၂-အပြည့်စုံဆုံး-automated-security-baseline-script)
3. [အဆင့်ဆင့် လုပ်ဆောင်နည်း (Step-by-Step Execution)](#၃-အဆင့်ဆင့်-လုပ်ဆောင်နည်း)
4. [စနစ်စစ်ဆေးခြင်းနှင့် အတည်ပြုခြင်း](#၄-စနစ်စစ်ဆေးခြင်းနှင့်-အတည်ပြုခြင်း)
5. [Proxy Server (zin-decenzed-node) တင်ဆင်ရန် အသင့်ဖြစ်မှု](#၅-proxy-server-zin-decenzed-node-တပ်ဆင်ခြင်း)

---

## ၁. ကြုံတွေ့ရတတ်သော ပြဿနာများနှင့် ဖြေရှင်းချက်များ

Ubuntu 24.04 (Noble) VPS အသစ်များတွင် အဖြစ်အများဆုံး အခက်အခဲများနှင့် ဖြေရှင်းနည်းများကို Script တွင် အလိုအလျောက် ထည့်သွင်းပေးထားပါသည်-

| ပြဿနာ (Issue) | အကြောင်းရင်း (Root Cause) | ဖြေရှင်းချက် (Solution in Script) |
| :--- | :--- | :--- |
| **`Connection failed [IP: 91.189.91.82 80]`** | VPS ပေါ်တွင် IPv6 မငြိမ်ခြင်းကြောင့် APT က IPv6 ဖြင့် ဆွဲရန်ကြိုးစားပြီး ပျက်ကျခြင်း | `Acquire::ForceIPv4 "true";` ကို ကြိုတင်ထည့်သွင်းခြင်း |
| **`0% [Waiting for headers]` ရပ်နေခြင်း** | Ubuntu default `archive.ubuntu.com` စာမျက်နှာ မအားလပ်ခြင်း သို့မဟုတ် Cloud Provider က Port 80 ပိတ်ထားခြင်း | တရားဝင် HTTPS Kernel Mirror (`https://mirrors.kernel.org/ubuntu/`) သို့ Auto ပြောင်းပေးခြင်း |
| **DNS Resolution Timeout** | VPS ၏ default DNS server မတုံ့ပြန်ခြင်း | `1.1.1.1` (Cloudflare) နှင့် `8.8.8.8` (Google DNS) သို့ အလိုအလျောက် သတ်မှတ်ပေးခြင်း |
| **SSH Port ပြောင်းသော်လည်း မပြောင်းခြင်း (Ubuntu 24.04)** | Ubuntu 24.04 တွင် SSH ကို `ssh.socket` ဖြင့် စတင်ထားခြင်း | `ssh.socket` ကို disable လုပ်ပြီး `ssh.service` ဖြင့် သီးသန့် အသက်သွင်းပေးခြင်း |

---

## ၂. အပြည့်စုံဆုံး Automated Security Baseline Script

အောက်ပါ Script သည် အထက်ဖော်ပြပါ Error အားလုံးကို အလိုအလျောက် ရှင်းလင်းပေးပြီး အောက်ပါ Security Setup များကို အပြည့်အစုံ ပြုလုပ်ပေးမည်ဖြစ်သည်-
- ✅ **Timezone**: Asia/Yangon (+06:30)
- ✅ **Swap**: 4GB Swap File (RAM အားဖြည့်ရန်နှင့် Crash မဖြစ်စေရန်)
- ✅ **Sudo User**: သီးသန့် User အသစ်ဆောက်ပြီး Password ခံထားခြင်း
- ✅ **SSH Hardening**: Custom Port (`2213`)၊ Root Login ပိတ်ခြင်း၊ Password Login ပိတ်ပြီး SSH Key Auth သာ ခွင့်ပြုခြင်း
- ✅ **UFW Firewall**: Default Inbound ပိတ်ပြီး SSH (`2213`), Web (`80, 443`), Hysteria/WireGuard (`58210/udp`) နှင့် Proxy Node (`8443, 32000-38000/tcp`) များ ကြိုတင်ဖွင့်ပေးခြင်း
- ✅ **Fail2ban**: SSH Brute-force တိုက်ခိုက်မှုများကို 24 နာရီ Auto Ban စနစ်
- ✅ **Auto Upgrades & Monitoring**: Unattended Security Updates နှင့် vnStat bandwidth monitor တပ်ဆင်ပေးခြင်း

---

## ၃. အဆင့်ဆင့် လုပ်ဆောင်နည်း

### အဆင့် (၁) - VPS သို့ Root ဖြင့် Login ဝင်ပြီး Script ရေးသားခြင်း

Terminal တွင် အောက်ပါ Code အားလုံးကို Copy ကူးပြီး Paste ချပါ:

```bash
cat > /root/safenet-security-baseline.sh <<'EOF'
#!/usr/bin/env bash
set -euo pipefail

USER_NAME="${USER_NAME:-zinko}"
SSH_PORT="${SSH_PORT:-2213}"
PUBLIC_KEY="${1:-${PUBLIC_KEY:-}}"

if [ -z "$PUBLIC_KEY" ]; then
  echo "=================================================================="
  echo "ERROR: SSH public key is required."
  echo "Usage: USER_NAME=zinko SSH_PORT=2213 /root/safenet-security-baseline.sh 'ssh-ed25519 AAAA...'"
  echo "=================================================================="
  exit 1
fi

if ! echo "$PUBLIC_KEY" | grep -Eq '^(ssh-ed25519|ssh-rsa|ecdsa-sha2-nistp256) '; then
  echo "ERROR: PUBLIC_KEY does not look like a valid SSH public key."
  exit 1
fi

echo "==> [Step 1/8] Fixing APT Repositories, DNS & Forcing IPv4"
# DNS Fallback သတ်မှတ်ခြင်း
sudo rm -f /etc/resolv.conf
echo -e "nameserver 1.1.1.1\nnameserver 8.8.8.8" > /etc/resolv.conf

# APT Configuration ပြင်ဆင်ခြင်း
mkdir -p /etc/apt/apt.conf.d
cat <<APTCONF > /etc/apt/apt.conf.d/99custom-fix
Acquire::ForceIPv4 "true";
Acquire::http::Timeout "30";
Acquire::https::Timeout "30";
APTCONF

# Ubuntu 24.04 Noble အတွက် တရားဝင် HTTPS Kernel Official Mirror သို့ ပြောင်းလဲခြင်း
rm -rf /etc/apt/sources.list /etc/apt/sources.list.d/* /var/lib/apt/lists/*
cat <<SOURCES > /etc/apt/sources.list.d/ubuntu.sources
Types: deb
URIs: https://mirrors.kernel.org/ubuntu/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: https://mirrors.kernel.org/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
SOURCES

echo "==> [Step 2/8] Setting Timezone to Asia/Yangon (+06:30)"
timedatectl set-timezone Asia/Yangon

echo "==> [Step 3/8] Configuring 4GB Swap File"
if [ ! -f /swapfile ]; then
  fallocate -l 4G /swapfile || dd if=/dev/zero of=/swapfile bs=1M count=4096
  chmod 600 /swapfile
  mkswap /swapfile
  swapon /swapfile
  
  if ! grep -q '/swapfile' /etc/fstab; then
    echo '/swapfile none swap sw 0 0' >> /etc/fstab
  fi
  
  if ! grep -q 'vm.swappiness' /etc/sysctl.conf; then
    echo 'vm.swappiness=10' >> /etc/sysctl.conf
  else
    sed -i 's/^vm.swappiness.*/vm.swappiness=10/' /etc/sysctl.conf
  fi
  sysctl -p || true
  echo "Swap file created successfully."
else
  echo "Swap file already exists. Skipping."
fi

echo "==> [Step 4/8] Updating System & Installing Essential Tools"
export DEBIAN_FRONTEND=noninteractive
apt update -y
apt upgrade -y
apt install -y ufw fail2ban unattended-upgrades htop iotop vnstat net-tools curl wget speedtest-cli || apt install -y ufw fail2ban unattended-upgrades htop iotop vnstat net-tools curl wget
apt autoremove -y
apt autoclean

echo "==> [Step 5/8] Creating Sudo User: $USER_NAME"
if ! id "$USER_NAME" >/dev/null 2>&1; then
  adduser --gecos "" "$USER_NAME"
fi
usermod -aG sudo "$USER_NAME"

# SSH Key ထည့်သွင်းခြင်း
install -d -m 700 -o "$USER_NAME" -g "$USER_NAME" "/home/$USER_NAME/.ssh"
echo "$PUBLIC_KEY" > "/home/$USER_NAME/.ssh/authorized_keys"
chmod 600 "/home/$USER_NAME/.ssh/authorized_keys"
chown -R "$USER_NAME:$USER_NAME" "/home/$USER_NAME/.ssh"

echo "==> [Step 6/8] SSH Hardening on Port $SSH_PORT"
cp /etc/ssh/sshd_config "/etc/ssh/sshd_config.bak.$(date +%Y%m%d%H%M%S)"

# Cloud Image SSH overrides များကို ပိတ်ခြင်း
for f in /etc/ssh/sshd_config.d/60-cloudimg-settings.conf /etc/ssh/sshd_config.d/01-cloud-init.conf /etc/ssh/sshd_config.d/50-cloud-init.conf; do
  if [ -f "$f" ]; then
    mv "$f" "$f.disabled"
  fi
done

cat > /etc/ssh/sshd_config.d/99-safenet-hardening.conf <<SSHCONF
Port $SSH_PORT
PasswordAuthentication no
PermitRootLogin no
KbdInteractiveAuthentication no
ChallengeResponseAuthentication no
PubkeyAuthentication yes
SSHCONF
chmod 644 /etc/ssh/sshd_config.d/99-safenet-hardening.conf

mkdir -p /run/sshd
chmod 0755 /run/sshd
sshd -t

# Ubuntu 24.04 socket activation ကို service သို့ ပြောင်းလဲခြင်း
systemctl disable --now ssh.socket || true
systemctl enable --now ssh.service
systemctl restart ssh

echo "==> [Step 7/8] Configuring UFW Firewall"
ufw default deny incoming
ufw default allow outgoing
ufw allow "$SSH_PORT/tcp" comment 'Custom SSH Port'
ufw allow 80/tcp comment 'HTTP'
ufw allow 443/tcp comment 'HTTPS'
ufw allow 58210/udp comment 'WireGuard/Hysteria'
ufw allow 8443/tcp comment 'VLESS Proxy'
ufw allow 32000:38000/tcp comment 'Trojan and Shadowsocks'
ufw --force enable

echo "==> [Step 8/8] Configuring Fail2ban & Services"
cat > /etc/fail2ban/jail.local <<JAIL
[DEFAULT]
backend = systemd
banaction = ufw

[sshd]
enabled = true
port = $SSH_PORT
maxretry = 3
findtime = 600
bantime = 86400
JAIL
systemctl enable --now fail2ban
systemctl restart fail2ban || true

dpkg-reconfigure -f noninteractive unattended-upgrades || true
systemctl enable --now unattended-upgrades || true
systemctl enable --now vnstat || true

echo
echo "=========================================="
echo "      🎉 SYSTEM BASELINE COMPLETED 🎉     "
echo "=========================================="
echo "Timezone: $(timedatectl | grep 'Time zone' | awk '{print $3}')"
echo "Swap: $(swapon --show | tail -n 1 | awk '{print $3}')"
echo "SSH Port: $SSH_PORT"
echo "User: $USER_NAME"
echo "UFW Firewall: Active with Proxy ports open"
echo "Fail2ban: Active (Protecting SSH on port $SSH_PORT)"
echo "=========================================="
echo "IMPORTANT: Test SSH in a new terminal window before closing this session:"
echo "ssh -p $SSH_PORT $USER_NAME@YOUR_SERVER_IP"
echo "=========================================="
EOF

chmod +x /root/safenet-security-baseline.sh
```

---

### အဆင့် (၂) - Script ကို စတင် Run ခြင်း

သင့်စိတ်ကြိုက် **Username**, **SSH Port** နှင့် **SSH Public Key** ကို ထည့်သွင်း၍ Run လိုက်ပါ:

```bash
USER_NAME="zinko" SSH_PORT="2213" /root/safenet-security-baseline.sh "ssh-ed25519 AAAA...သင့်_Public_Key_အပြည့်အစုံ"
```

> **သတိပြုရန်**: Script Run နေစဉ် User အသစ်အတွက် **Password** တောင်းခံပါက သင့်စိတ်ကြိုက် Password ကို ၂ ကြိမ် ရိုက်ထည့်ပေးပါ။

---

## ၄. စနစ်စစ်ဆေးခြင်းနှင့် အတည်ပြုခြင်း

Script အောင်မြင်စွာ ပြီးဆုံးပြီး `DONE` ဟု ပြပါက မူလ Terminal ကို ချက်ချင်း မပိတ်ပါနှင့်ဦး။

မိမိ Local PC ၏ Terminal အသစ်တစ်ခု ဖွင့်ကာ အောက်ပါအတိုင်း ဝင်ရောက်စမ်းသပ်ပါ:

```bash
ssh -p 2213 zinko@YOUR_SERVER_IP
```

အကယ်၍ SSH Key ဖြင့် အောင်မြင်စွာ ဝင်ရောက်နိုင်ပြီဆိုပါက Root session ကို စိတ်ချစွာ ပိတ်နိုင်ပါပြီ။

---

## ၅. Proxy Server (`zin-decenzed-node`) တပ်ဆင်ခြင်း

လုံခြုံရေး Baseline ပြီးဆုံးသွားသည့် VPS တွင် `zin-decenzed-node` ကို အလွယ်တကူ တင်နိုင်ပါပြီ-

```bash
# ၁။ Directory ဆောက်ပြီး Binary ရယူခြင်း
mkdir -p /opt/decenzed-node && cd /opt/decenzed-node
wget -O decenzed-node https://github.com/icecube092/decenzed-node/releases/latest/download/decenzed-node-linux-amd64
chmod +x decenzed-node

# System PATH ထဲသို့ ထည့်သွင်းခြင်း (ဘယ်နေရာကမဆို ခေါ်သုံးနိုင်ရန်)
sudo ln -sf /opt/decenzed-node/decenzed-node /usr/local/bin/decenzed-node

# ၂။ Setup စတင် run ခြင်း
decenzed-node setup

# ၃။ ချိတ်ဆက်ရန် Link ရယူခြင်း
decenzed-node link
```
*(UFW Firewall တွင် Port `8443` နှင့် `32000-38000` များကို Baseline Script က ကြိုတင်ဖွင့်ပေးထားပြီး ဖြစ်သဖြင့် Firewall ပြန်ဖွင့်ရန် မလိုပါ)*

### 🛠️ ဆာဗာ စီမံခန့်ခွဲမှု Commands များ
- **လည်ပတ်နေမှုနှင့် အပြင်မှ ချိတ်ဆက်နိုင်မှု စစ်ဆေးရန်**: `decenzed-node check`
- **Data Traffic စာရင်းကြည့်ရန်**: `decenzed-node stats`
- **User အသစ် ထပ်ဖန်တီးရန်**: `decenzed-node link add friend1`
- **User ပယ်ဖျက်ရန်**: `decenzed-node link remove friend1`
- **Realtime Log ကြည့်ရှုရန်**: `decenzed-node logs xray -f`
- **Background Service Restart ချရန်**: `decenzed-node service restart`

---

## ၆. စနစ်တစ်ခုလုံးကို လုံးဝ ဖယ်ထုတ် ဖျက်ဆီးနည်း (Uninstallation & Cleanup)

အကယ်၍ `zin-decenzed-node` ကို Server ပေါ်မှ အပြီးအပိုင် ရှင်းလင်းလိုပါက:

```bash
# ၁။ Service ကို ရပ်တန့်ပြီး ဖယ်ရှားပါ
/opt/decenzed-node/decenzed-node service uninstall
sudo systemctl stop decenzed-node 2>/dev/null || true
sudo systemctl disable decenzed-node 2>/dev/null || true
sudo rm -f /etc/systemd/system/decenzed-node.service
sudo systemctl daemon-reload

# ၂။ Binary နှင့် Data ဖိုင်အားလုံးကို ဖျက်ဆီးပါ
sudo rm -rf /opt/decenzed-node
sudo rm -f /usr/local/bin/decenzed-node

# ၃။ UFW Firewall တွင် Proxy Port များကို ပြန်ပိတ်ပါ
sudo ufw delete allow 8443/tcp
sudo ufw delete allow 32000:38000/tcp
sudo ufw reload
```
