# Better IP Use (BIU)

报告问题及发布：https://github.com/my-txz/better_ip_use/issues

Reporting issues and publishing:https://github.com/my-txz/better_ip_use/issues

![Spigot 1.19–1.20.4](https://img.shields.io/badge/Bukkit-1.19%E2%80%931.21.11-00AAFF?logo=minecraft)  
![Java 17](https://img.shields.io/badge/Java-17+-red)
![Java 21](https://img.shields.io/badge/Java-21+-blue)

A lightweight, conflict-resistant Minecraft plugin that displays player geolocation and filters inappropriate chat using **built-in integrations with Uapipro APIs**. Features include smart location formatting, sensitive word detection, chat cooldowns, and a community-driven reporting system.

一款轻量级、抗冲突的 Minecraft 插件，通过**内置 Uapipro API 集成**实现玩家地域显示与聊天内容过滤。包含智能地域格式化、敏感词检测、发言冷却及社区举报系统。

> 🙏 Special thanks to **[Uapipro](https://uapis.cn)** for providing reliable public APIs used in this plugin.  
> 🙏 特别鸣谢 **[Uapipro](https://uapis.cn)** 提供稳定可靠的公共 API 支持。

---

# Tips:
###  Version download corresponds to:
###  1.X supports 1.19-1.20.6  |  2.X supports 1.20.6-1.21.11 | 3. X supports 1.13-1.18.2 (in development)
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
# 语言设置: zh / en
language: "zh"

# 功能开关
settings:
  # 启用聊天合规检查
  enable_chat_check: true
  # 启用预审核模式 (true = 发送后审核再显示, false = 实时审核拦截)
  # true: 玩家发送 -> 服务器审核 -> 通过则广播显示, 不通过则屏蔽
  # false: 玩家发送 -> 实时检测 -> 违规则拦截并取消事件
  pre_moderation: true
  # 启用 IP 显示 (Tab/头顶/聊天)
  enable_ip_display: true

# 聊天冷却设置
chat_cooldown:
  enabled: true
  # 冷却时间 (秒)
  seconds: 5

# 举报系统设置
report_system:
  enabled: true
  # 检查几分钟内的消息
  check_minutes_ago: 5
  # 举报冷却时间 (秒)
  reporter_cooldown_seconds: 300
  # 每次最多检查几条消息
  max_check_messages: 3

# API 配置
api:
  sensitive_word:
    # 正确的API端点 - 使用uapis.cn的敏感词检测接口
    url: "https://uapis.cn/api/v1/text/profanitycheck"
  ip_info:
    # IP信息查询接口模板
    url_template: "https://uapis.cn/api/v1/network/ipinfo?ip=%IP%"

# 显示设置
display:
  region_format: "auto" # auto (取前两级) 或 full (完整)
  color: "&3"
  unknown_text: "Unknown"

  # Tab 列表后缀
  tab_suffix_enabled: true
  # 聊天后缀
  chat_suffix_enabled: true
  # 头顶前缀
  nametag_prefix_enabled: true

# 处罚设置
punishment:
  warn_first: true
  mute_minutes: 5
  broadcast_on_mute: true

# 消息多语言配置
messages:
  zh:
    prefix: "&3[&bBIU&3] &r"
    warn: "&c您的发言包含不合规内容，请注意文明用语。"
    cooldown: "&c请勿频繁发言，还需等待 &e%time% &c秒。"
    mute: "&c你因多次违规已被禁言 %mute_minutes% 分钟。"
    mute_admin: "&c你已被管理员禁言。"
    mute_active: "&c你当前处于禁言状态，无法发言。"
    mute_broadcast: "&c玩家 &e%player% &c因多次违规已被禁言 %mute_minutes% 分钟。"
    unmuted: "&a你已被解除禁言，可以正常发言了。"
    unmuted_auto: "&a你的禁言时间已到，已自动解除禁言。"
    reload: "&a配置已重载。"
    cache_cleared: "&aIP地址缓存已清除，所有玩家显示已重置。"
    no_permission: "&c你没有权限执行此操作。"
    help: |
      &a/biu help &7- 显示帮助
      &a/biu reload &7- 重载配置
      &a/biu clearcache &7- 清除IP缓存
      &a/biu report <玩家> &7- 举报玩家近期违规发言
      &a/biu mute <玩家> &7- 手动禁言玩家
      &a/biu unmute <玩家> &7- 解除玩家禁言
      &a/biu check <文本> &7- 检测文本是否违规
    report_usage: "&c用法: /biu report <玩家名>"
    report_disabled: "&c举报系统当前已禁用。"
    report_cooldown: "&c举报功能冷却中，还需等待 &e%time% &c秒。"
    report_self: "&c不能举报自己。"
    report_target_no_history: "&c该玩家近期无可举报的发言记录。"
    report_checking: "&a正在检查玩家 &e%player% &a的近期发言..."
    report_success_compliant: "&a举报检查结果：玩家 &e%player% &a近期发言合规。"
    report_success_forbidden: "&c举报检查结果：玩家 &e%player% &c存在违规发言，已自动禁言。"
    report_broadcast: "&c玩家 &e%player% &c因被举报违规发言，已被系统禁言。举报者: &e%reporter%"
    mute_cmd_usage: "&c用法: /biu mute <玩家名>"
    unmute_cmd_usage: "&c用法: /biu unmute <玩家名>"
    player_muted: "&c已手动禁言玩家 &e%player%&c。"
    player_unmuted: "&a已解禁玩家 &e%player%&a。"
    player_not_found: "&c未找到玩家: &e%player%&c。"

  en:
    prefix: "&3[&bBIU&3] &r"
    warn: "&cYour message contains non-compliant content. Please be civil."
    cooldown: "&cPlease do not spam. Wait &e%time% &cseconds."
    mute: "&cYou have been muted for %mute_minutes% minutes due to repeated violations."
    mute_admin: "&cYou have been muted by an administrator."
    mute_active: "&cYou are currently muted and cannot chat."
    mute_broadcast: "&cPlayer &e%player% &chas been muted for %mute_minutes% minutes."
    unmuted: "&aYou have been unmuted. You can chat normally now."
    unmuted_auto: "&aYour mute time has expired. You can chat normally now."
    reload: "&aConfiguration reloaded."
    cache_cleared: "&aIP address cache cleared."
    no_permission: "&cYou do not have permission."
    help: |
      &a/biu help &7- Show help
      &a/biu reload &7- Reload config
      &a/biu clearcache &7- Clear IP cache
      &a/biu report <player> &7- Report a player for recent bad chat
      &a/biu mute <player> &7- Manually mute a player
      &a/biu unmute <player> &7- Unmute a player
      &a/biu check <text> &7- Check if text violates rules
    report_usage: "&cUsage: /biu report <player>"
    report_disabled: "&cReport system is currently disabled."
    report_cooldown: "&cReport function is on cooldown. Wait &e%time% &cseconds."
    report_self: "&cYou cannot report yourself."
    report_target_no_history: "&cTarget player has no recent chat history to report."
    report_checking: "&aChecking recent messages from &e%player%&a..."
    report_success_compliant: "&aReport Result: Player &e%player% &ais compliant."
    report_success_forbidden: "&cReport Result: Player &e%player% &cviolated rules. Auto-muted."
    report_broadcast: "&cPlayer &e%player% &cwas muted due to report. Reporter: &e%reporter%"
    mute_cmd_usage: "&cUsage: /biu mute <player>"
    unmute_cmd_usage: "&cUsage: /biu unmute <player>"
    player_muted: "&cPlayer &e%player%&c has been manually muted."
    player_unmuted: "&aPlayer &e%player%&c has been unmuted."
    player_not_found: "&cPlayer not found: &e%player%&c."
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

>  No telemetry, no ads, no hidden features.  
> 无遥测、无广告、无隐藏功能。  
>  
> **Complies with Modrinth Community Guidelines.**  
> **遵守 Modrinth 社区规范。**
