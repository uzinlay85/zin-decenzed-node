# zin-decenzed-node တပ်ဆင်အသုံးပြုနည်း လမ်းညွှန်အပြည့်အစုံ (Setup Guide)

`zin-decenzed-node` သည် Single Binary (Go ဖြင့် ရေးသားထားသော) ကိုယ်ပိုင် VLESS, Trojan နှင့် Shadowsocks Proxy Server ဖြစ်သည်။ ၎င်းတွင် embedded `xray-core` ပါဝင်ပြီး Linux VPS ဆာဗာ၊ အိမ်သုံးကွန်ပျူတာ သို့မဟုတ် Router ပေါ်တွင် အလွယ်တကူ run နိုင်ကာ မိမိကိုယ်ပိုင် VPN/Proxy Node အဖြစ် လုံခြုံစွာ အသုံးပြုနိုင်သည်။

---

## မာတိကာ (Table of Contents)
1. [အဓိက အင်္ဂါရပ်များ (Key Features)](#၁-အဓိက-အင်္ဂါရပ်များ-key-features)
2. [ကြိုတင်လိုအပ်ချက်များ (Prerequisites)](#၂-ကြိုတင်လိုအပ်ချက်များ-prerequisites)
3. [⭐ ဦးစားပေး နံပါတ် ၁ - Linux VPS Server တွင် အစအဆုံး တပ်ဆင်နည်း](#၃--ဦးစားပေး-နံပါတ်-၁---linux-vps-server-တွင်-အစအဆုံး-တပ်ဆင်နည်း)
   - [အဆင့် ၁ - VPS သို့ SSH ဖြင့် Login ဝင်ခြင်း](#အဆင့်-၁---vps-သို့-ssh-ဖြင့်-login-ဝင်ခြင်း)
   - [အဆင့် ၂ - VPS Firewall နှင့် Cloud Security Group ဖွင့်ခြင်း](#အဆင့်-၂---vps-firewall-နှင့်-cloud-security-group-ဖွင့်ခြင်း)
   - [အဆင့် ၃ - Binary ဒေါင်းလုဒ်ရယူခြင်း](#အဆင့်-၃---binary-ဒေါင်းလုဒ်ရယူခြင်း)
   - [အဆင့် ၄ - Setup Wizard ဖြင့် Node စတင်သတ်မှတ်ခြင်း](#အဆင့်-၄---setup-wizard-ဖြင့်-node-စတင်သတ်မှတ်ခြင်း)
   - [အဆင့် ၅ - Background Service အဖြစ် တပ်ဆင်ခြင်း](#အဆင့်-၅---background-service-အဖြစ်-တပ်ဆင်ခြင်း)
   - [အဆင့် ၆ - ဆာဗာချိတ်ဆက်မှု စစ်ဆေးခြင်း](#အဆင့်-၆---ဆာဗာချိတ်ဆက်မှု-စစ်ဆေးခြင်း)
4. [Camouflage Mode (၂) မျိုးအကြောင်း](#၄-camouflage-mode-၂-မျိုးအကြောင်း)
5. [အခြား Platform များတွင် တပ်ဆင်ခြင်း (Windows, OpenWRT, macOS)](#၅-အခြား-platform-များတွင်-တပ်ဆင်ခြင်း)
   - [OpenWRT Router ပေါ်တွင် တိုက်ရိုက်တပ်ဆင်ခြင်း](#openwrt-router-ပေါ်တွင်-တိုက်ရိုက်တပ်ဆင်ခြင်း)
   - [Windows ပေါ်တွင် တပ်ဆင်ခြင်း](#windows-ပေါ်တွင်-တပ်ဆင်ခြင်း)
   - [Source Code မှ Build လုပ်ခြင်း](#source-code-မှ-build-လုပ်ခြင်း)
6. [Client များထည့်သွင်းခြင်းနှင့် ဖုန်း/ကွန်ပျူတာတွင် ချိတ်ဆက်နည်း](#၆-client-များထည့်သွင်းခြင်းနှင့်-ဖုန်းကွန်ပျူတာတွင်-ချိတ်ဆက်နည်း)
7. [Node အခြေအနေ စစ်ဆေးခြင်းနှင့် ထိန်းသိမ်းခြင်း (Management Commands)](#၇-node-အခြေအနေ-စစ်ဆေးခြင်းနှင့်-ထိန်းသိမ်းခြင်း-management-commands)
8. [အဖြစ်များသော ပြဿနာများနှင့် ဖြေရှင်းနည်းများ (Troubleshooting & FAQ)](#၈-အဖြစ်များသော-ပြဿနာများနှင့်-ဖြေရှင်းနည်းများ-troubleshooting--faq)

---

## ၁. အဓိက အင်္ဂါရပ်များ (Key Features)

- **Protocols စုံလင်စွာပါဝင်ခြင်း**: VLESS (XTLS-Vision), Trojan, Shadowsocks classic (chacha20-ietf-poly1305) နှင့် Shadowsocks-2022 တို့ကို ထောက်ပံ့သည်။
- **Camouflage စနစ် (၂) မျိုး**: REALITY သို့မဟုတ် ကိုယ်ပိုင် Built-in Website + Let's Encrypt TLS (Stealth Web Fallback)။
- **Auto SSL Certificate**: TLS Mode အတွက် Let's Encrypt SSL ကို DuckDNS DNS-01 challenge ဖြင့် Auto ရယူ/သက်တမ်းတိုးပေးခြင်း (Port 80 ဖွင့်ရန်မလိုပါ)။
- **One Subscription Link per Client**: Client တစ်ဦးစီအတွက် Link တစ်ခုတည်းဖြင့် Protocols အားလုံး (VLESS, Trojan, Shadowsocks) ကို Auto ဆွဲယူနိုင်ခြင်း။
- **Bandwidth & Speed Cap Control**: တစ်ဦးချင်းအလိုက် Speed Limit သတ်မှတ်နိုင်ခြင်းနှင့် Traffic Usage စာရင်းကြည့်ရှုနိုင်ခြင်း။
- **Dynamic DNS (DDNS)**: DuckDNS integration ပါဝင်သဖြင့် Dynamic IP ဆာဗာများတွင်လည်း IP ကို အလိုအလျောက် Update ပြုလုပ်ပေးခြင်း။
- **Standalone & Single Binary**: External database သို့မဟုတ် ဗဟိုဆာဗာ မလိုဘဲ သီးခြားလွတ်လပ်စွာ အလုပ်လုပ်ခြင်း။
- **Cross-Platform & Router Support**: Linux VPS (Ubuntu, Debian, CentOS), OpenWRT Router (ARM, MIPS, x86), Windows, macOS များအားလုံးတွင် အသုံးပြုနိုင်ခြင်း။

---

## ၂. ကြိုတင်လိုအပ်ချက်များ (Prerequisites)

1. **Linux VPS Server (အကြံပြုချက်)**:
   - Ubuntu 20.04/22.04/24.04, Debian 11/12, CentOS/Rocky Linux စသည့် Linux OS ရှိသော VPS တစ်ခု (ဥပမာ- DigitalOcean, Hetzner, Vultr, Linode, AWS, Oracle Cloud)။
   - Static Public IP တစ်ခု အလိုအလျောက် ရရှိထားပြီးဖြစ်၍ Port Forwarding ပြုလုပ်ရန် မလိုပါ။
2. **DuckDNS Subdomain (TLS Mode အသုံးပြုလိုပါက)**:
   - [duckdns.org](https://www.duckdns.org) တွင် အကောင့်ဖွင့်ပြီး Subdomain တစ်ခု (ဥပမာ- `myvpnnode.duckdns.org`) နှင့် Token ကို ရယူထားပါ။ (REALITY Mode သုံးပါက မလိုအပ်ပါ)။
3. **Root / Sudo Access**:
   - ဆာဗာတွင် command များ run ရန် root သို့မဟုတ် sudo permission လိုအပ်ပါသည်။

---

## ၃. ⭐ ဦးစားပေး နံပါတ် ၁ - Linux VPS Server တွင် အစအဆုံး တပ်ဆင်နည်း

Linux VPS တွင် တပ်ဆင်ခြင်းသည် Port Forwarding ရှုပ်ထွေးမှုများ မရှိဘဲ Static Public IP ရရှိထားသောကြောင့် အကောင်းဆုံးနှင့် အလွယ်ကူဆုံး နည်းလမ်းဖြစ်ပါသည်။

### အဆင့် ၁ - VPS သို့ SSH ဖြင့် Login ဝင်ခြင်း
မိမိ ကွန်ပျူတာ (Terminal သို့မဟုတ် PowerShell သို့မဟုတ် PuTTY) မှတစ်ဆင့် VPS သို့ SSH ဝင်ပါ:
```bash
ssh root@YOUR_VPS_IP
```
*(root မဟုတ်ပါက `ssh username@YOUR_VPS_IP` ဝင်ပြီး `sudo -i` ပြုလုပ်ပါ)*

---

### အဆင့် ၂ - VPS Firewall နှင့် Cloud Security Group ဖွင့်ခြင်း

ဆာဗာ OS အတွင်းရှိ Firewall (UFW) တွင် လိုအပ်သော Port များကို ဖွင့်ပေးပါ:

```bash
# Ubuntu / Debian အတွက် UFW ဖွင့်နည်း
sudo apt update && sudo apt install -y ufw curl wget

# SSH port မပိတ်သွားစေရန် အရင်ဖွင့်ပါ
sudo ufw allow 22/tcp

# VLESS Port (Default: 8443)
sudo ufw allow 8443/tcp

# Trojan Port Range (ရွေးချယ်အသုံးပြုပါက)
sudo ufw allow 32000:35000/tcp

# Shadowsocks Port Range (ရွေးချယ်အသုံးပြုပါက)
sudo ufw allow 35000:38000/tcp

# Firewall ကို အသက်သွင်းပါ
sudo ufw enable
```

> [!IMPORTANT]
> **Cloud Provider Firewall (AWS / Oracle Cloud / Hetzner / Google Cloud) သတိပြုရန်:**
> အကယ်၍ သင့် VPS သည် Oracle Cloud, AWS သို့မဟုတ် Google Cloud ဖြစ်ပါက Cloud Provider Console ၏ **Security Lists / Ingress Rules** တွင် TCP Port `8443`, `32000-38000` များကို Inbound Allow ပြုလုပ်ပေးရပါမည်။

---

### အဆင့် ၃ - Binary ဒေါင်းလုဒ်ရယူခြင်း

ဆာဗာ၏ သီးသန့် Directory တစ်ခုဖန်တီးပြီး binary ကို ဒေါင်းလုဒ်ဆွဲပါ:

```bash
# Directory အသစ်ဆောက်ပြီး ဝင်ပါ
mkdir -p /opt/decenzed-node && cd /opt/decenzed-node

# x86_64 (Intel/AMD) VPS အတွက် ဒေါင်းလုဒ်ဆွဲခြင်း
wget -O decenzed-node https://github.com/icecube092/decenzed-node/releases/latest/download/decenzed-node-linux-amd64

# (ARM64 VPS ဖြစ်ပါက အောက်ပါ command ကို သုံးပါ)
# wget -O decenzed-node https://github.com/icecube092/decenzed-node/releases/latest/download/decenzed-node-linux-arm64

# Execution Permission ပေးပါ
chmod +x decenzed-node
```

---

### အဆင့် ၄ - Setup Wizard ဖြင့် Node စတင်သတ်မှတ်ခြင်း

Interactive Setup Wizard ကို စတင် run ပါ:
```bash
./decenzed-node setup
```

Wizard မေးခွန်းများကို အောက်ပါအတိုင်း အလွယ်တကူ ဖြေဆိုသွားပါ-

1. **Network Readiness Check**: ဆာဗာ၏ Public IP နှင့် Country (ဥပမာ- `SG [VLESS]`) ကို အလိုအလျောက် ပြသပေးမည်။
2. **VLESS Port**: Default `8443` ကို Enter နှိပ်၍ ရွေးပါ။
3. **Extra Protocols (ရွေးချယ်နိုင်သည်)**:
   - Trojan ဖွင့်လိုပါက `y` (Port default: 32000–35000 ကြား)
   - Shadowsocks ဖွင့်လိုပါက `y` (Port default: 35000–38000 ကြား)
4. **Policy Settings**:
   - `bittorrent` block လုပ်မလား (`Enter` = ပိတ်မည်၊ `no` = မပိတ်ပါ)
   - Per-user speed cap (`Enter` = 50 Mbit/s၊ `no` = ကန့်သတ်ချက်မထားပါ)
   - DuckDNS / Domain သုံးမလား:
     - **REALITY သုံးမည်ဆိုပါက**: `no` ဟု ရိုက်ထည့်ပါ။
     - **TLS Mode သုံးမည်ဆိုပါက**: `yes` ဟု ရိုက်ပြီး DuckDNS Token နှင့် Subdomain နာမည် (ဥပမာ- `mynode`) ရိုက်ထည့်ပါ။
5. **Camouflage Mode ရွေးချယ်ခြင်း**:
   - **`reality` (အလွယ်ဆုံးနှင့် အကြံပြုချက်)**: Local အနီးရှိ TLS 1.3 / HTTP/2 website ကို scan ဖတ်ကာ REALITY keypair အလိုအလျောက် ထုတ်ပေးမည်။
   - **`tls`**: DuckDNS domain ဖြင့် Let's Encrypt တရားဝင် SSL Certificate ရယူကာ Built-in Decoy Website အနောက်တွင် ဖုံးကွယ်မည်။
6. **Client Setup**: ပထမဆုံး အသုံးပြုသူ Client အမည် (ဥပမာ- `admin` သို့မဟုတ် `user1`) ကို ရိုက်ထည့်ပါ။

---

### အဆင့် ၅ - Background Service အဖြစ် တပ်ဆင်ခြင်း

Setup ၏ နောက်ဆုံးအဆင့်တွင် Background Service install လုပ်မလားဟု မေးပါက `yes` ရွေးပါ။ သို့မဟုတ် command ဖြင့် တိုက်ရိုက် install လုပ်နိုင်ပါသည်:

```bash
./decenzed-node service install
```

System Service စတင်လည်ပတ်နေမှုကို စစ်ဆေးရန်:
```bash
./decenzed-node service status
```

---

### အဆင့် ၆ - ဆာဗာချိတ်ဆက်မှု စစ်ဆေးခြင်း

ဆာဗာကောင်းမွန်စွာ လည်ပတ်နေပြီး အပြင်မှ လှမ်းချိတ်နိုင်ခြင်း ရှိ/မရှိ စစ်ဆေးပါ:
```bash
./decenzed-node check
```
*(အကယ်၍ အားလုံး စိမ်းလန်းပြီး Reachable ဟု ပြပါက သင့် Proxy Node အောင်မြင်စွာ တပ်ဆင်ပြီးစီးပါပြီ)*

---

### အဆင့် ၇ - Client Link ထုတ်ယူပြီး ချိတ်ဆက်အသုံးပြုခြင်း

```bash
# Subscription Link သို့မဟုတ် Raw Link များ ထုတ်ယူရန်
./decenzed-node link

# Protocol တစ်ခုချင်းစီ၏ Raw URI (vless://, trojan://, ss://) ကြည့်ရန်
./decenzed-node link -l

# Sing-box Outbound JSON ကြည့်ရန်
./decenzed-node link -s
```

---

## ၄. Camouflage Mode (၂) မျိုးအကြောင်း

`setup` ပြုလုပ်ချိန်တွင် အောက်ပါ Camouflage နည်းလမ်း (၂) ခုအနက် တစ်ခုကို ရွေးချယ်နိုင်ပါသည်-

| အင်္ဂါရပ် | REALITY Mode (Default) | TLS + Built-in Website Mode |
| :--- | :--- | :--- |
| **Domain လိုအပ်မှု** | မလိုအပ်ပါ (Third-party live site ကို scan ဖတ်သည်) | DuckDNS Subdomain လိုအပ်ပါသည် |
| **SSL Certificate** | မလိုပါ (Reality Keys ဖြင့် အလုပ်လုပ်သည်) | Let's Encrypt DNS-01 ဖြင့် Auto ရယူပေးသည် |
| **Camouflage ပုံစံ** | အခြားပြင်ပ TLS 1.3 ဆိုက် (SNI) ကို ဟန်ဆောင်သည် | မိမိဆာဗာအတွင်း built-in Decoy Website ကို တင်ထားပြီး xray မှ fallback လုပ်သည် |
| **Subscription Link** | Single subscription link မရပါ (Per-protocol raw links ထုတ်ပေးသည်) | `https://domain:port/sub/client-id` ဖြင့် Subscription Link ရရှိသည် |
| **အသင့်တော်ဆုံး အခြေအနေ** | Domain မဝယ်ချင်သူများ၊ အလွယ်တကူ Setup လုပ်ချင်သူများ | Stealth အပြည့်အဝလိုချင်သူများနှင့် Client များကို Subscription ဖြင့် အလွယ်တကူ Link ပေးလိုသူများ |

---

## ၅. အခြား Platform များတွင် တပ်ဆင်ခြင်း

### OpenWRT Router ပေါ်တွင် တိုက်ရိုက်တပ်ဆင်ခြင်း
Router ပေါ်တွင် တိုက်ရိုက် Node အဖြစ် Run လိုပါက Terminal (SSH) မှ အောက်ပါ command တစ်ကြောင်းတည်း run လိုက်ရုံဖြင့် CPU Arch ကို auto-detect လုပ်ကာ binary ဒေါင်းလုဒ်ဆွဲပြီး firewall rule ပါ တစ်ခါတည်း ဖွင့်ပေးပါသည်-

```bash
wget -O - https://github.com/icecube092/decenzed-node/releases/latest/download/install-openwrt.sh | sh
```

ထို့နောက် Router ပေါ်တွင် Setup စတင်ပါ-
```bash
decenzed-node setup
```

> **Router အသုံးပြုသူများအတွက် အကြံပြုချက်:**
> - Flash Memory 8MB/16MB သာရှိသော Router များတွင် USB တပ်ဆင်ပြီး Extroot သို့မဟုတ် `DECENZED_DATA=/mnt/usb/decenzed-data` ဟု သတ်မှတ်အသုံးပြုပါ။

---

### Windows ပေါ်တွင် တပ်ဆင်ခြင်း
1. Release မှ `decenzed-node-windows-amd64.exe` ကို ဒေါင်းလုဒ်ရယူပြီး နာမည်ကို `decenzed-node.exe` ဟု ပြောင်းပါ။
2. သီးသန့် Folder တစ်ခု (ဥပမာ- `C:\decenzed-node\`) တွင် ထည့်ပါ။
3. `decenzed-node.exe` ကို Right Click နှိပ်ပြီး **"Run as Administrator"** ဖြင့် ဖွင့်ပါ။
4. Interactive Shell တွင် `setup` ဟု ရိုက်ထည့်ကာ စတင်ပါ။
5. အိမ်သုံး Router တွင် TCP Port `8443` (နှင့် အခြားဖွင့်ထားသော Port များ) ကို မိမိ PC Local IP သို့ **Port Forwarding** ပြုလုပ်ပေးပါ။

---

### Source Code မှ Build လုပ်ခြင်း
Go 1.26+ ရှိပါက source မှ တိုက်ရိုက် build လုပ်နိုင်ပါသည်-
```bash
git clone https://github.com/uzinlay85/zin-decenzed-node.git
cd zin-decenzed-node/src
go build -o decenzed-node ./cmd/decenzed-node
```

---

## ၆. Client များထည့်သွင်းခြင်းနှင့် ဖုန်း/ကွန်ပျူတာတွင် ချိတ်ဆက်နည်း

### ၁။ အသုံးပြုသူ (User) အသစ် ထည့်သွင်းခြင်း / ပယ်ဖျက်ခြင်း
- သူငယ်ချင်းအတွက် Client အသစ်ဖန်တီးရန်:
  ```bash
  ./decenzed-node link add friend1
  ```
- Client အဟောင်းကို ပယ်ဖျက်ရန် (Access ပိတ်ရန်):
  ```bash
  ./decenzed-node link remove friend1
  ```
*(User အသစ်ထည့်ခြင်း/ဖျက်ခြင်း ပြုလုပ်ပါက Background Service သည် အလိုအလျောက် Reload ပြုလုပ်ပေးပါသည်)*

### ၂။ ချိတ်ဆက်အသုံးပြုနိုင်သော App များ
- **Android**: `v2rayNG`, `NekoBox`, `sing-box`, `Hiddify`
- **Windows**: `v2rayN`, `NekoBox for Windows`, `Hiddify-Next`, `sing-box`
- **iOS / macOS**: `FoXray`, `Shadowrocket`, `Streisand`, `sing-box`, `V2Box`

> **ချိတ်ဆက်နည်း**: 
> App ထဲသို့ ဝင်ရောက်ပြီး `+` (သို့မဟုတ်) `Add Subscription / Import from Clipboard` ဖြင့် ရရှိထားသော Link ကို ထည့်သွင်းကာ ချိတ်ဆက်အသုံးပြုနိုင်ပါသည်။

---

## ၇. Node အခြေအနေ စစ်ဆေးခြင်းနှင့် ထိန်းသိမ်းခြင်း (Management Commands)

| Command | လုပ်ဆောင်ချက် ဖော်ပြချက် |
| :--- | :--- |
| `./decenzed-node check` | အပြင်မှ Node Port များသို့ ချိတ်ဆက်၍ ရ/မရ၊ IP နှင့် Speed ကို စစ်ဆေးခြင်း |
| `./decenzed-node stats` | အသုံးပြုနေသော Protocol များ၊ Client တစ်ဦးချင်းအလိုက် အသုံးပြုထားသော Data Traffic (MB/GB) စာရင်းကြည့်ခြင်း |
| `./decenzed-node logs` | Node နှင့် Xray ၏ နောက်ဆုံး Log များကို ကြည့်ရှုခြင်း |
| `./decenzed-node logs xray -f` | Xray မှ Realtime log များကို တိုက်ရိုက် ကြည့်ရှုခြင်း (`q` နှိပ်၍ ထွက်နိုင်သည်) |
| `./decenzed-node debug` | အသေးစိတ် Debug Log စနစ်ကို ဖွင့်/ပိတ် ပြုလုပ်ခြင်း |
| `./decenzed-node config node` | App Config ဖိုင် အသေးစိတ်ကို ကြည့်ရှုခြင်း |
| `./decenzed-node config xray` | Xray core ၏ `xray.json` ဖိုင်ကို ကြည့်ရှုခြင်း |
| `./decenzed-node update` | Version အသစ် ရှိ/မရှိ စစ်ဆေးပြီး အလိုအလျောက် Update လုပ်ခြင်း |
| `./decenzed-node service status` | Background Service ၏ လက်ရှိ Run နေမှု အခြေအနေကို စစ်ဆေးခြင်း |
| `./decenzed-node service restart`| Background Service ကို Restart ချခြင်း |
| `./decenzed-node service uninstall`| Background Service အဖြစ် သတ်မှတ်ထားခြင်းကို ဖြုတ်ချခြင်း |
| `./decenzed-node setup` | Setting များ (Port, Camouflage, Protocols) ကို ပြန်လည်ပြင်ဆင်သတ်မှတ်ခြင်း |

---

## ၈. အဖြစ်များသော ပြဿနာများနှင့် ဖြေရှင်းနည်းများ (Troubleshooting & FAQ)

### ၁။ `decenzed-node check` လုပ်ချိန်တွင် Port Unreachable ဟု ပြနေခြင်း
- **အကြောင်းအရင်း**: VPS OS Firewall (UFW) သို့မဟုတ် Cloud Provider ၏ Security Group/Firewall တွင် Inbound Port မဖွင့်ရသေးခြင်းကြောင့် ဖြစ်သည်။
- **ဖြေရှင်းနည်း**:
  - `sudo ufw allow 8443/tcp` ပြုလုပ်ထားကြောင်း စစ်ဆေးပါ။
  - Cloud Console (AWS / Oracle Cloud / Google Cloud / Hetzner) ၏ Ingress Firewall Rules တွင် TCP Port `8443` (သို့မဟုတ် သတ်မှတ်ထားသော Port များ) ကို Allow ပြုလုပ်ပေးပါ။

### ၂။ TLS Mode တွင် SSL Certificate ရယူရာတွင် Error တက်ခြင်း
- DuckDNS Token မှန်ကန်မှု ရှိ/မရှိ စစ်ဆေးပါ။
- Subdomain ထည့်သွင်းရာတွင် `.duckdns.org` မပါဘဲ ရှေ့နာမည် (ဥပမာ- `myvpnnode`) ကိုသာ ထည့်ရပါမည်။
- DuckDNS ဆာဗာတွင် DNS record update ဖြစ်ရန် ၁ မိနစ်ခန့် စောင့်ဆိုင်းပြီးမှ ပြန်လည်စမ်းသပ်ပါ။

### ၃။ OpenWRT တွင် Storage မလောက်ငှခြင်း
- 32-bit ARM နှင့် MIPS Router များအတွက် Release Binary များသည် UPX ဖိသိပ်ထားသဖြင့် Disk ပေါ်တွင် 10–12 MB သာ ယူပါသည်။
- Flash သေးငယ်သော Router များတွင် USB Drive တပ်ဆင်ကာ `DECENZED_DATA=/mnt/usb/decenzed-data` ဟု သတ်မှတ်၍ အသုံးပြုပါ။

---

## အချုပ် (Conclusion)

`zin-decenzed-node` သည် Linux VPS ပေါ်တွင် အလွန်ပေါ့ပါးစွာ Run နိုင်ပြီး Xray-core ကို အခြေခံထားသောကြောင့် မြန်မာနိုင်ငံကဲ့သို့ အင်တာနက် ပိတ်ဆို့မှုများပြားသော ပတ်ဝန်းကျင်တွင် REALITY နှင့် Stealth TLS Camouflage နည်းပညာများဖြင့် လွတ်လပ်လုံခြုံစွာ အင်တာနက် အသုံးပြုနိုင်ရန် အထူးသင့်လျော်သော Proxy Server ဖြေရှင်းချက်တစ်ခု ဖြစ်ပါသည်။
