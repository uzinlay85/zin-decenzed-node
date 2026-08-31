# zin-decenzed-node တပ်ဆင်အသုံးပြုနည်း လမ်းညွှန်အပြည့်အစုံ (Setup Guide)

`zin-decenzed-node` သည် Single Binary (Go ဖြင့် ရေးသားထားသော) ကိုယ်ပိုင် VLESS, Trojan နှင့် Shadowsocks Proxy Server ဖြစ်သည်။ ၎င်းတွင် embedded `xray-core` ပါဝင်ပြီး ဆာဗာ သို့မဟုတ် Router ပေါ်တွင် အလွယ်တကူ run နိုင်ကာ မိမိကိုယ်ပိုင် VPN/Proxy Node အဖြစ် အသုံးပြုနိုင်သည်။

---

## မာတိကာ (Table of Contents)
1. [အဓိက အင်္ဂါရပ်များ (Key Features)](#၁-အဓိက-အင်္ဂါရပ်များ-key-features)
2. [ကြိုတင်လိုအပ်ချက်များ (Prerequisites)](#၂-ကြိုတင်လိုအပ်ချက်များ-prerequisites)
3. [Camouflage Mode (၂) မျိုးအကြောင်း](#၃-camouflage-mode-၂-မျိုးအကြောင်း)
4. [စတင်ဒေါင်းလုဒ်ရယူခြင်းနှင့် တပ်ဆင်ခြင်း (Installation)](#၄-စတင်ဒေါင်းလုဒ်ရယူခြင်းနှင့်-တပ်ဆင်ခြင်း-installation)
   - [Windows ပေါ်တွင် တပ်ဆင်ခြင်း](#windows-ပေါ်တွင်-တပ်ဆင်ခြင်း)
   - [Linux / macOS ပေါ်တွင် တပ်ဆင်ခြင်း](#linux--macos-ပေါ်တွင်-တပ်ဆင်ခြင်း)
   - [OpenWRT Router ပေါ်တွင် တိုက်ရိုက်တပ်ဆင်ခြင်း](#openwrt-router-ပေါ်တွင်-တိုက်ရိုက်တပ်ဆင်ခြင်း)
   - [Source Code မှ Build လုပ်ခြင်း](#source-code-မှ-build-လုပ်ခြင်း)
5. [အဆင့်ဆင့် Setup ပြုလုပ်နည်း (Interactive Setup Wizard)](#၅-အဆင့်ဆင့်-setup-ပြုလုပ်နည်း-interactive-setup-wizard)
6. [Router Port Forwarding ချိန်ညှိခြင်း](#၆-router-port-forwarding-ချိန်ညှိခြင်း)
7. [Client အသုံးပြုသူများထည့်သွင်းခြင်းနှင့် ချိတ်ဆက်နည်း](#၇-client-အသုံးပြုသူများထည့်သွင်းခြင်းနှင့်-ချိတ်ဆက်နည်း)
8. [Node အခြေအနေ စစ်ဆေးခြင်းနှင့် ထိန်းသိမ်းခြင်း (Management Commands)](#၈-node-အခြေအနေ-စစ်ဆေးခြင်းနှင့်-ထိန်းသိမ်းခြင်း-management-commands)
9. [အဖြစ်များသော ပြဿနာများနှင့် ဖြေရှင်းနည်းများ (Troubleshooting & FAQ)](#၉-အဖြစ်များသော-ပြဿနာများနှင့်-ဖြေရှင်းနည်းများ-troubleshooting--faq)

---

## ၁. အဓိက အင်္ဂါရပ်များ (Key Features)

- **Protocols စုံလင်စွာပါဝင်ခြင်း**: VLESS (XTLS-Vision), Trojan, Shadowsocks classic (chacha20-ietf-poly1305) နှင့် Shadowsocks-2022 တို့ကို ထောက်ပံ့သည်။
- **Camouflage စနစ် (၂) မျိုး**: REALITY သို့မဟုတ် ကိုယ်ပိုင် Built-in Website + Let's Encrypt TLS (Stealth Web Fallback)။
- **Auto SSL Certificate**: TLS Mode အတွက် Let's Encrypt SSL ကို DuckDNS DNS-01 challenge ဖြင့် Auto ရယူ/သက်တမ်းတိုးပေးခြင်း (Port 80 ဖွင့်ရန်မလိုပါ)။
- **One Subscription Link per Client**: Client တစ်ဦးစီအတွက် Link တစ်ခုတည်းဖြင့် Protocols အားလုံး (VLESS, Trojan, Shadowsocks) ကို Auto ဆွဲယူနိုင်ခြင်း။
- **Bandwidth & Speed Cap Control**: တစ်ဦးချင်းအလိုက် Speed Limit သတ်မှတ်နိုင်ခြင်းနှင့် Traffic Usage စာရင်းကြည့်ရှုနိုင်ခြင်း။
- **Dynamic DNS (DDNS)**: DuckDNS integration ပါဝင်သဖြင့် IP ပြောင်းသွားသော်လည်း အလိုအလျောက် Update ပြုလုပ်ပေးခြင်း။
- **Standalone & Single Binary**: External database သို့မဟုတ် ဗဟိုဆာဗာ မလိုဘဲ သီးခြားလွတ်လပ်စွာ အလုပ်လုပ်ခြင်း။
- **Cross-Platform & Router Support**: Windows, macOS, Linux သာမက OpenWRT Router (ARM, MIPS, x86) များပေါ်တွင် တိုက်ရိုက် run နိုင်ခြင်း။

---

## ၂. ကြိုတင်လိုအပ်ချက်များ (Prerequisites)

1. **Public IP (တိုက်ရိုက် အင်တာနက် IP)**:
   - Static IP ဖြစ်စေ၊ Dynamic IP ဖြစ်စေ ရရှိနိုင်ရပါမည်။
   - **သတိပြုရန်**: CGNAT (Carrier-Grade NAT) အောက်တွင် ရှိနေပါက အပြင်မှ Inbound Connection ဝင်ရောက်နိုင်မည် မဟုတ်ပါ။ ISP ထံမှ Public / Real IP တောင်းဆိုရန် သို့မဟုတ် VPS ဆာဗာတစ်ခု ရှေ့ခံရန် လိုအပ်ပါသည်။
2. **Fixed LAN IP (DHCP Reservation)**:
   - မိမိ၏ PC/Server/Router ကို Router ၏ DHCP Settings တွင် Fixed IP (MAC Bind) ပြုလုပ်ထားရန် လိုအပ်ပါသည်။ သို့မှသာ Reboot ကျချိန်တွင် LAN IP မပြောင်းဘဲ Port Forward ပေါက်လွဲမည်မဟုတ်ပါ။
3. **ဖွင့်ထားသော TCP Port များ**:
   - VLESS အတွက် အဓိက Port: Default `8443` (သို့မဟုတ် မိမိစိတ်ကြိုက် Port)
   - Trojan ဖွင့်လိုပါက Port အပိုတစ်ခု: Default Range `32000–35000`
   - Shadowsocks ဖွင့်လိုပါက Port အပိုတစ်ခု: Default Range `35000–38000`
4. **DuckDNS Subdomain (TLS Mode အသုံးပြုလိုပါက)**:
   - [duckdns.org](https://www.duckdns.org) တွင် အကောင့်ဖွင့်ပြီး Subdomain တစ်ခု (ဥပမာ- `myvpnnode.duckdns.org`) နှင့် Token ကို ရယူထားပါ။
5. **Administrator / Root Permissions**:
   - Background System Service အဖြစ် တပ်ဆင်ရန် Admin/Root 권한 လိုအပ်ပါသည်။

---

## ၃. Camouflage Mode (၂) မျိုးအကြောင်း

`setup` ပြုလုပ်ချိန်တွင် အောက်ပါ Camouflage နည်းလမ်း (၂) ခုအနက် တစ်ခုကို ရွေးချယ်နိုင်ပါသည်-

| အင်္ဂါရပ် | REALITY Mode (Default) | TLS + Built-in Website Mode |
| :--- | :--- | :--- |
| **Domain လိုအပ်မှု** | မလိုအပ်ပါ (Auto scan third-party site) | DuckDNS Subdomain လိုအပ်ပါသည် |
| **SSL Certificate** | မိမိကိုယ်တိုင် ရယူရန် မလိုပါ | Let's Encrypt DNS-01 ဖြင့် Auto ရယူပေးသည် |
| **Camouflage ပုံစံ** | အခြားပြင်ပ TLS 1.3 ဆိုက် (SNI) ကို ဟန်ဆောင်အသုံးချသည် | မိမိဆာဗာအတွင်း built-in Decoy Website ကို တင်ထားပြီး xray မှ fallback လုပ်သည် |
| **Subscription Link** | Single subscription link မရပါ (Per-protocol raw links ထုတ်ပေးသည်) | `https://domain:port/sub/client-id` ဖြင့် Subscription Link ရရှိသည် |
| **အသင့်တော်ဆုံး အခြေအနေ** | Domain မဝယ်ချင်သူများ၊ အလွယ်တကူ Setup လုပ်ချင်သူများ | Stealth အပြည့်အဝလိုချင်သူများနှင့် Client များကို Subscription ဖြင့် အလွယ်တကူ Link ပေးလိုသူများ |

---

## ၄. စတင်ဒေါင်းလုဒ်ရယူခြင်းနှင့် တပ်ဆင်ခြင်း (Installation)

### Windows ပေါ်တွင် တပ်ဆင်ခြင်း
1. Release မှ `decenzed-node-windows-amd64.exe` ကို ဒေါင်းလုဒ်ရယူပြီး နာမည်ကို `decenzed-node.exe` ဟု ပြောင်းပါ။
2. သီးသန့် Folder တစ်ခု (ဥပမာ- `C:\decenzed-node\`) တွင် ထည့်ပါ။ (အချက်အလက်များကို ထို Folder ထဲရှိ `decenzed-data/` တွင် သိမ်းဆည်းမည်ဖြစ်သည်)
3. `decenzed-node.exe` ကို Right Click နှိပ်ပြီး **"Run as Administrator"** ဖြင့် ဖွင့်ပါ (သို့မဟုတ် Terminal/PowerShell မှ run ပါ)။
4. ဖွင့်လိုက်ပါက Interactive Shell ပေါ်လာမည်ဖြစ်ပြီး `setup` ဟု ရိုက်ထည့်ကာ စတင်နိုင်ပါသည်။

### Linux / macOS ပေါ်တွင် တပ်ဆင်ခြင်း
1. မိမိ CPU architecture နှင့် ကိုက်ညီသော binary (ဥပမာ- `decenzed-node-linux-amd64` သို့မဟုတ် `decenzed-node-darwin-arm64`) ကို ဒေါင်းလုဒ်ရယူပါ။
2. Execution Permission ပေးပြီး Run ပါ-
   ```bash
   chmod +x decenzed-node
   sudo ./decenzed-node setup
   ```

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
> - Flash Memory (Storage) 8MB/16MB သာရှိသော Router များတွင် USB တပ်ဆင်ပြီး Extroot သို့မဟုတ် `DECENZED_DATA=/mnt/usb/decenzed-data` ဟု သတ်မှတ်အသုံးပြုပါ။

### Source Code မှ Build လုပ်ခြင်း
Go 1.26+ ရှိပါက source မှ တိုက်ရိုက် build လုပ်နိုင်ပါသည်-
```bash
git clone https://github.com/uzinlay85/zin-decenzed-node.git
cd zin-decenzed-node/src
go build -o decenzed-node ./cmd/decenzed-node
```

---

## ၅. အဆင့်ဆင့် Setup ပြုလုပ်နည်း (Interactive Setup Wizard)

`decenzed-node setup` ကို run လိုက်ပါက အောက်ပါအတိုင်း အဆင့်ဆင့် မေးမြန်းသွားမည်ဖြစ်သည်-

```text
==========================================
   decenzed-node interactive setup
==========================================
```

### အဆင့် ၁ - Network Readiness Check
1. **Public IP & Country Detection**: ဆာဗာ၏ Public IP နှင့် နိုင်ငံကုဒ် (ဥပမာ- `MM [VLESS]`) ကို အလိုအလျောက် ရှာဖွေပေးမည်။ (CGNAT ဖြစ်နေပါက သတိပေးချက် ပြပါမည်)
2. **VLESS TCP Port**: Default ဖြစ်သော `8443` (သို့မဟုတ် အခြား Port) ကို ရွေးပါ။
3. **Port Check**: သတ်မှတ်ထားသော Port သည် အပြင်မှ ဝင်ရောက်နိုင်ခြင်း ရှိ/မရှိ ယာယီ Listener ဖွင့်၍ စစ်ဆေးပေးမည်။
4. **Speed Test**: Connection Upload/Download Speed ကို စမ်းသပ်ပေးမည်။

### အဆင့် ၂ - Extra Protocols ရွေးချယ်ခြင်း
- **Trojan**: ဖွင့်လိုပါက `y` နှိပ်ပြီး Port (Default: 32000–35000 ကြား) သတ်မှတ်ပါ။
- **Shadowsocks**: Classic chacha20-ietf-poly1305 ဖွင့်လိုပါက `y` နှိပ်ပြီး Port (Default: 35000–38000 ကြား) သတ်မှတ်ပါ။
- **Shadowsocks-2022**: 2022-blake3-aes-128-gcm ကို လိုအပ်ပါက `y` နှိပ်၍ သီးခြား Port ဖြင့် ဖွင့်နိုင်ပါသည်။

### အဆင့် ၃ - မူဝါဒနှင့် ကန့်သတ်ချက်များ (Policies)
- **Blocked protocols**: Default မှာ `bittorrent` ဖြစ်သည်။ Torrent မပိတ်လိုပါက `no` ဟု ရိုက်ပါ။
- **Per-user speed cap**: အသုံးပြုသူတစ်ဦးချင်းစီ၏ အမြန်နှုန်းကန့်သတ်ချက် (Default: `50 Mbit/s`၊ ကန့်သတ်ချက်မထားလိုပါက `no`)။
- **DDNS / Domain Configuration**:
  - DuckDNS သုံးမည်ဆိုပါက `yes` နှိပ်ပြီး DuckDNS Token နှင့် Subdomain (ဥပမာ- `mynode` ဟုသာရိုက်ပါ၊ `.duckdns.org` ထည့်ရန်မလို) ရိုက်ထည့်ပါ။
  - ကိုယ်ပိုင် Domain ရှိပါက Domain name ကို ရိုက်ထည့်ပါ။
  - Domain မသုံးပါက `no` ဟု ရိုက်ပါ။

### အဆင့် ၄ - Camouflage Mode ရွေးချယ်ခြင်း
- **`reality`**: Local အနီးရှိ TLS 1.3 / HTTP/2 website များကို scan ဖတ်ပြီး REALITY keys များကို အလိုအလျောက် ထုတ်ပေးမည်။
- **`tls`**: DuckDNS domain ပေါ်တွင် Let's Encrypt မှ တရားဝင် SSL Certificate ကို DNS-01 ACME challenge ဖြင့် ရယူပေးမည် (Email လိပ်စာ ရိုက်ထည့်ပေးရန် လိုအပ်ပါသည်)။

### အဆင့် ၅ - Client ဖန်တီးခြင်းနှင့် Service စတင်ခြင်း
- ပထမဆုံး အသုံးပြုသူ Client အမည်ကို ဖန်တီးပေးမည်။
- `xray.json` config ကို အလိုအလျောက် ထုတ်ပေးမည်။
- စက်စဖွင့်သည်နှင့် Background Service အဖြစ် Auto Run စေရန် Install ပြုလုပ်ပေးပြီး ပထမဆုံး Share Link ကို ထုတ်ပေးပါမည်။

---

## ၆. Router Port Forwarding ချိန်ညှိခြင်း

အကယ်၍ `decenzed-node` ကို အိမ်သုံး PC/Home Server ပေါ်တွင် တင်ထားပါက Router ထဲသို့ ဝင်ရောက်၍ Port Forwarding (Virtual Server) ပြုလုပ်ပေးရမည်-

1. Router Admin Dashboard (192.168.1.1 သို့မဟုတ် 192.168.0.1) သို့ ဝင်ပါ။
2. **Port Forwarding / NAT / Virtual Server** သို့ သွားပါ။
3. အောက်ပါ Port များကို Node run ထားသော စက်၏ Local IP သို့ ညွှန်ပေးပါ:
   - **VLESS Port**: TCP `8443` -> Local IP: `8443`
   - **Trojan Port (ဖွင့်ထားပါက)**: TCP `33001` -> Local IP: `33001`
   - **Shadowsocks Port (ဖွင့်ထားပါက)**: TCP `36001` -> Local IP: `36001`
4. **OpenWRT Router ပေါ်တွင် တိုက်ရိုက် run ထားပါက**:
   - Setup Script က `Allow-decenzed-node` Firewall Rule ကို အလိုအလျောက် ထည့်သွင်းပေးပြီး ဖြစ်သဖြင့် Port Forwarding ထပ်လုပ်ရန် မလိုပါ။

---

## ၇. Client အသုံးပြုသူများထည့်သွင်းခြင်းနှင့် ချိတ်ဆက်နည်း

### ၁။ Share Links များ ထုတ်ယူခြင်း
```bash
decenzed-node link
```
- **TLS Mode တွင်**: အသုံးပြုသူတစ်ဦးအတွက် Single Subscription Link (`https://yourdomain.duckdns.org:8443/sub/<client-id>`) ကို ထုတ်ပေးမည်။
- **REALITY Mode တွင်**: VLESS / Trojan / Shadowsocks raw links များကို ထုတ်ပေးမည်။

အခြား Option များ-
- `decenzed-node link -l` : Protocol တစ်ခုချင်းစီ၏ Raw URI links (vless://, trojan://, ss://) များကို ကြည့်ရန်။
- `decenzed-node link -s` : Sing-box အသုံးပြုသူများအတွက် JSON Outbound format ဖြင့် ထုတ်ယူရန်။

### ၂။ အသုံးပြုသူ အသစ်ထည့်ခြင်း / ပယ်ဖျက်ခြင်း
- သူငယ်ချင်းအတွက် Client အသစ်ဖန်တီးရန်:
  ```bash
  decenzed-node link add friend1
  ```
- Client အဟောင်းကို ပယ်ဖျက်ရန် (Access ပိတ်ရန်):
  ```bash
  decenzed-node link remove friend1
  ```
*(Client အသစ်ထည့်ခြင်း/ဖျက်ခြင်း ပြုလုပ်ပါက Background Service သည် အလိုအလျောက် Config reload ပြုလုပ်ပေးပါသည်)*

### ၃။ Client Software များတွင် ချိတ်ဆက်နည်း
- **Android**: `v2rayNG`, `NekoBox`, `sing-box`, `Hiddify`
- **Windows**: `v2rayN`, `NekoBox for Windows`, `Hiddify-Next`, `sing-box`
- **iOS / macOS**: `FoXray`, `Shadowrocket`, `Streisand`, `sing-box`, `V2Box`

> **အသုံးပြုပုံ**: Client App ထဲသို့ ဝင်ရောက်ပြီး `+` (သို့မဟုတ်) `Subscription Group` တွင် မိမိရရှိထားသော Subscription Link ကို ထည့်သွင်း၍ **Update Subscription** ပြုလုပ်ပေးရုံဖြင့် Node များ အားလုံး ရောက်ရှိလာမည် ဖြစ်ပါသည်။

---

## ၈. Node အခြေအနေ စစ်ဆေးခြင်းနှင့် ထိန်းသိမ်းခြင်း (Management Commands)

`decenzed-node` interactive prompt တွင် သို့မဟုတ် command line မှ အောက်ပါ command များကို အသုံးပြုနိုင်ပါသည်-

| Command | လုပ်ဆောင်ချက် ဖော်ပြချက် |
| :--- | :--- |
| `decenzed-node check` | အပြင်မှ Node Port ပေါက်များသို့ ချိတ်ဆက်၍ ရ/မရ၊ IP နှင့် Speed ကို ပြန်လည်စစ်ဆေးခြင်း |
| `decenzed-node stats` | အသုံးပြုနေသော Protocol များ၊ Client တစ်ဦးချင်းအလိုက် အသုံးပြုထားသော Data Traffic (MB/GB) စာရင်းကြည့်ခြင်း |
| `decenzed-node logs` | Node နှင့် Xray ၏ နောက်ဆုံး Log များကို ကြည့်ရှုခြင်း |
| `decenzed-node logs xray -f` | Xray မှ Realtime log များကို တိုက်ရိုက် ကြည့်ရှုခြင်း (`q` နှိပ်၍ ထွက်နိုင်သည်) |
| `decenzed-node debug` | အသေးစိတ် Debug Log စနစ်ကို ဖွင့်/ပိတ် ပြုလုပ်ခြင်း |
| `decenzed-node config node` | App Config ဖိုင် အသေးစိတ်ကို ကြည့်ရှုခြင်း |
| `decenzed-node config xray` | Xray core ၏ `xray.json` ဖိုင်ကို ကြည့်ရှုခြင်း |
| `decenzed-node update` | Version အသစ် ရှိ/မရှိ စစ်ဆေးပြီး အလိုအလျောက် Update လုပ်ခြင်း |
| `decenzed-node service status` | Background Service ၏ လက်ရှိ Run နေမှု အခြေအနေကို စစ်ဆေးခြင်း |
| `decenzed-node service restart`| Background Service ကို Restart ချခြင်း |
| `decenzed-node service uninstall`| Background Service အဖြစ် သတ်မှတ်ထားခြင်းကို ပြန်လည်ဖြုတ်ချခြင်း |
| `decenzed-node setup` | Setting များ (Port, Camouflage, Protocols) ကို ပြန်လည်ပြင်ဆင်သတ်မှတ်ခြင်း |

---

## ၉. အဖြစ်များသော ပြဿနာများနှင့် ဖြေရှင်းနည်းများ (Troubleshooting & FAQ)

### ၁။ `decenzed-node check` လုပ်ချိန်တွင် Port Unreachable ဟု ပြနေခြင်း
- **အကြောင်းအရင်း**: Router တွင် Port Forwarding မလုပ်ရသေးခြင်း၊ Firewall မှ ပိတ်ထားခြင်း သို့မဟုတ် CGNAT ဖြစ်နေခြင်းကြောင့် ဖြစ်နိုင်သည်။
- **ဖြေရှင်းနည်း**:
  - Router ထဲတွင် Node run ထားသော စက်၏ Local IP သို့ TCP Port (8443) ကို Port Forwarding သေချာပေါက် ဖွင့်ထားကြောင်း စစ်ဆေးပါ။
  - Windows Defender Firewall သို့မဟုတ် OS Firewall တွင် Inbound Port ခွင့်ပြုထားကြောင်း စစ်ဆေးပါ။
  - မိမိဖုန်း၏ Mobile Data ဖြင့် Client App မှတစ်ဆင့် တိုက်ရိုက်ချိတ်ဆက် စမ်းသပ်ကြည့်ပါ (LAN အတွင်း Loopback check သည် Router အမျိုးအစားပေါ် မူတည်၍ တစ်ခါတစ်ရံ မအောင်မြင်နိုင်ပါ)။

### ၂။ ISP က CGNAT ခံထားပါက မည်သို့လုပ်ရမည်နည်း။
- အကယ်၍ `decenzed-node check` က သင့် IP သည် CGNAT/Private IP ဖြစ်နေကြောင်း ပြသပါက ပြင်ပမှ တိုက်ရိုက် လှမ်းချိတ်၍ မရနိုင်ပါ။
- **ဖြေရှင်းနည်း**: ISP (အင်တာနက်ဝန်ဆောင်မှုပေးသူ) ထံသို့ Public IP / Real IP လျှောက်ထားပါ (သို့မဟုတ်) စျေးသက်သာသော Cloud VPS တစ်ခုတွင် `decenzed-node` ကို တင်၍ အသုံးပြုပါ။

### ၃။ TLS Mode တွင် SSL Certificate ရယူရာတွင် Error တက်ခြင်း
- DuckDNS Token မှန်ကန်မှု ရှိ/မရှိ ပြန်လည်စစ်ဆေးပါ။
- Subdomain ရိုက်ထည့်ရာတွင် `.duckdns.org` မပါဘဲ ရှေ့နာမည် (ဥပမာ- `myvpnnode`) ကိုသာ ရိုက်ထည့်ရပါမည်။
- DuckDNS ဆာဗာတွင် DNS record update ဖြစ်ရန် ၁ မိနစ်ခန့် စောင့်ဆိုင်းပြီးမှ ပြန်လည်စမ်းသပ်ပါ။

### ၄။ OpenWRT တွင် နေရာ (Disk Space) မလောက်ငှခြင်း
- 32-bit ARM နှင့် MIPS Router များအတွက် Release Binary များသည် UPX ဖိသိပ်ထားသဖြင့် Disk ပေါ်တွင် 10–12 MB သာ ယူပါသည်။
- သို့သော် Flash သေးငယ်သော Router များအတွက် USB Drive တစ်ခု တပ်ဆင်ကာ `DECENZED_DATA=/mnt/usb/decenzed-data` ဟု သတ်မှတ်၍ အသုံးပြုပါ။

---

## အချုပ် (Conclusion)

`zin-decenzed-node` သည် အလွန်ပေါ့ပါးပြီး Xray-core ကို အခြေခံထားသောကြောင့် မြန်မာနိုင်ငံကဲ့သို့ အင်တာနက် ပိတ်ဆို့မှုများပြားသော ပတ်ဝန်းကျင်တွင် REALITY နှင့် Stealth TLS Camouflage နည်းပညာများဖြင့် လွတ်လပ်လုံခြုံစွာ အင်တာနက် အသုံးပြုနိုင်ရန် အထူးသင့်လျော်သော Proxy Server ဖြေရှင်းချက်တစ်ခု ဖြစ်ပါသည်။
