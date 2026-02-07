# Better IP Use (BIU)

![Spigot 1.19–1.20.4](https://img.shields.io/badge/Spigot-1.19%E2%80%931.20.4-00AAFF?logo=minecraft)  
![Java 17](https://img.shields.io/badge/Java-17+-orange)

A lightweight, conflict-resistant Minecraft plugin that displays player geolocation and filters inappropriate chat using **built-in integrations with Uapipro APIs**. Features include smart location formatting, sensitive word detection, chat cooldowns, and a community-driven reporting system.

一款轻量级、抗冲突的 Minecraft 插件，通过**内置 Uapipro API 集成**实现玩家地域显示与聊天内容过滤。包含智能地域格式化、敏感词检测、发言冷却及社区举报系统。

> 🙏 Special thanks to **[Uapipro](https://uapis.cn)** for providing reliable public APIs used in this plugin.  
> 🙏 特别鸣谢 **[Uapipro](https://uapis.cn)** 提供稳定可靠的公共 API 支持。

---

## ✨ Core Features

### 🌍 Smart Geolocation Display
Fetches region data via `https://uapis.cn/api/v1/network/ipinfo?ip=%IP%` and formats it consistently across three interfaces:

- **Tab List**: Appends as suffix → `Steve [中国 北京]`  
- **Chat Message**: Rewrites format → `Steve[中国 北京]: Hello!`  
- **Nametag (Above Head)**: Uses prefix for visibility → `[中国 北京] Steve`  

By default, regions are auto-trimmed to the first two space-separated parts (e.g., `"中国 北京 海淀区"` → `"中国 北京"`). Configurable via `display.region_format: auto | full`.

### 🚫 Sensitive Word Filtering
Uses `https://uapis.cn/api/v1/sensitive-word/quick-check` to detect violations. A message is flagged if:
- Response contains `"status": "forbidden"`, **or**
- The `forbidden_words` array has ≥1 entry.

**Punishment flow**:
1. First offense → warning
2. Repeat offense → mute (default: 5 minutes)
3. Optional broadcast on mute

Bypass permission: `better_ip_use.bypass`.

### ⏳ Chat Cooldown System
Prevents spam by enforcing a configurable delay between messages (default: 5 seconds). Players see a countdown when violating.

### 📢 Community Reporting (`/biu report <player>`)
Players can report others for recent inappropriate speech:
- Checks up to **3 messages** from the last **5 minutes**
- Reporter cooldown: **5 minutes** (configurable)
- If any message violates rules, target is **automatically muted**
- Fully asynchronous — no server lag

### ♻️ Resource Efficiency & Cleanup
- IP-to-region results are cached per player
- All player data (cache, history, mutes) is cleaned on quit
- Manual cache clearing via `/biu clearcache`
- Chat history automatically expires after 10 minutes to save memory

### ⚙️ Conflict-Aware Event Handling
- **Display updates** use `EventPriority.MONITOR` — runs *after* plugins like Essentials/CMI
- **Chat formatting** uses `EventPriority.HIGH` — reliably overrides most chat systems

---

## ⚠️ Requirements

- **Minecraft**: Spigot/Paper **1.19 to 1.20.4** (inclusive)  
- **Java**: **17+**  
- **Internet Access**: Must reach `uapis.cn`

> ❗ This plugin **currently only supports** Java 17 and Minecraft 1.19–1.20.4.  
> ❗ 本插件**目前仅支持** Java 17 及 Minecraft 1.19–1.20.4。

> 🔧 **We are currently adapting BIU for Bukkit. You may try it at your own risk.**  
> 🔧 **我们正在对 Bukkit 进行适配，可以自行进行尝试。**

---

## 🔧 Configuration Highlights (`config.yml`)

```yaml
language: "zh"  # or "en"

settings:
  enable_chat_check: true
  enable_ip_display: true

chat_cooldown:
  enabled: true
  seconds: 5

report_system:
  enabled: true
  check_minutes_ago: 5
  reporter_cooldown_seconds: 300
  max_check_messages: 3

api:
  sensitive_word:
    url: "https://uapis.cn/api/v1/sensitive-word/quick-check"
  ip_info:
    url_template: "https://uapis.cn/api/v1/network/ipinfo?ip=%IP%"

display:
  region_format: "auto"
  color: "&3"
  unknown_text: "Unknown"
  tab_suffix_enabled: true
  chat_suffix_enabled: true
  nametag_prefix_enabled: true

punishment:
  warn_first: true
  mute_minutes: 5
  broadcast_on_mute: true
```

All display elements share the same color and fallback text.

---

## 📜 Commands & Permissions

| Command | Permission | Description |
|--------|------------|-------------|
| `/biu help` | `better_ip_use.use` | Show help |
| `/biu reload` | `better_ip_use.reload` | Reload config |
| `/biu clearcache` | `better_ip_use.reload` | Clear IP cache |
| `/biu report <player>` | `better_ip_use.use` | Report player’s recent chat |
| `/biu mute <player>` | `better_ip_use.admin` | Manually mute player |
| `/biu unmute <player>` | `better_ip_use.admin` | Unmute player |

Default permissions: ops only. Tab completion supported.

---

## 📦 Deployment

1. Place `BetterIPUse.jar` in `plugins/`
2. Start server to generate `config.yml`
3. Adjust settings if needed (defaults work out-of-box with Uapipro)
4. Use `/biu reload` after changes

No extra dependencies — uses Spigot’s built-in Gson.

---

> Made by a student developer. No telemetry, no ads, no hidden features.  
> 由学生开发者制作，无遥测、无广告、无隐藏功能。  
>  
> **Complies with Modrinth Community Guidelines.**  
> **遵守 Modrinth 社区规范。**
