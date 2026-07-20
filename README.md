# Clash-Rules

个人 Clash / OpenClash 规则集，托管在 GitHub，通过 `rule-providers` 订阅。
用于 iStoreOS 旁路由（192.168.77.100）上的 OpenClash。

**完全自建，不依赖任何第三方规则集订阅。**

## 🏗️ 架构

### A. 手写规则集（`rules/*.yaml`, `ip/*.yaml`）
按厂商/服务拆分，每个文件几十到几百行，可读可 diff 可手动维护：

| 文件 | 用途 |
|---|---|
| `rules/custom_proxy.yaml` | ⭐ 覆盖 cn_domain 误判（伪装 .cn 的境外代理域） |
| `rules/custom_direct.yaml` | ⭐ 你个人特有的直连补充 |
| `rules/custom_reject.yaml` | ⭐ 广告 / 遥测拦截 |
| `rules/ai.yaml` | ChatGPT / Claude / Gemini / Grok / Copilot |
| `rules/tiktok.yaml` | TikTok / CapCut |
| `rules/youtube.yaml` | YouTube |
| `rules/google.yaml` | Google 服务（不含 YouTube） |
| `rules/github.yaml` | GitHub |
| `rules/telegram.yaml` | Telegram |
| `rules/netflix.yaml` | Netflix |
| `rules/paypal.yaml` | PayPal |
| `rules/onedrive.yaml` | OneDrive / SharePoint |
| `rules/microsoft.yaml` | Microsoft / Windows / VSCode |
| `rules/apple.yaml` | Apple 全家桶（含 DNS 失败修复） |
| `rules/speedtest.yaml` | 测速服务 |
| `rules/private_domain.yaml` | .local / .lan / .arpa 等内网 TLD |
| `ip/private_ip.yaml` | RFC1918 内网段 |
| `ip/google_ip.yaml` | Google IP 段 |
| `ip/apple_ip.yaml` | Apple IP 段（AS714 主要段） |
| `ip/telegram_ip.yaml` | Telegram IP 段 |
| `ip/netflix_ip.yaml` | Netflix CDN IP 段 |

### B. 自动 mirror 规则集（`rules/mirror/*.yaml`, `ip/mirror/*.yaml`）
上万条的大规则集不可能手写，通过 GitHub Actions 每天从 `MetaCubeX/meta-rules-dat` 同步：

| 文件 | 上游 |
|---|---|
| `rules/mirror/cn_domain.yaml` | geosite/cn.yaml |
| `rules/mirror/geolocation-not-cn.yaml` | geosite/geolocation-!cn.yaml |
| `ip/mirror/cn_ip.yaml` | geoip/cn.yaml |

**同步机制**：`.github/workflows/mirror-upstream.yml`  每天 UTC 04:17 (北京 12:17) 自动跑，或在 Actions 页手动触发。数据存在你自己的仓库，上游挂了/被投毒也不影响。

## 🔗 订阅 URL

```
https://raw.githubusercontent.com/jadonxuan/Clash-Rules/main/<path>
```

国内加速备选：
```
https://ghproxy.com/https://raw.githubusercontent.com/jadonxuan/Clash-Rules/main/<path>
https://cdn.jsdelivr.net/gh/jadonxuan/Clash-Rules@main/<path>
```

## 🧭 规则优先级（rules 段顺序 = 优先级）

见 `examples/openclash-integration.yaml`，简版：

```
1. private_ip / private_domain          → DIRECT
2. custom_reject                        → REJECT
3. custom_proxy                         → 🚀 默认代理  （必须在 cn_domain 前面）
4. custom_direct                        → DIRECT
5. ai                                   → 🤖 ChatGPT
6. github / telegram / netflix / paypal → 🚀 默认代理
7. tiktok                               → 🎵 TikTok
8. youtube                              → 📹 YouTube
9. google / google_ip                   → 🍀 Google
10. apple / apple_ip                    → DIRECT (or PROXY)
11. microsoft / onedrive                → DIRECT (or PROXY)
12. speedtest                           → DIRECT
13. cn_domain / cn_ip                   → DIRECT
14. geolocation-not-cn                  → 🚀 默认代理
15. MATCH                               → 🐟 漏网之鱼
```

## 🩺 已解决的问题

- **hxaws.tnjxaon.cn / cha.onblix.com**：伪装 .cn 的境外代理服务端 → `custom_proxy`
- **token.safebrowsing.apple**：DNS 解析失败 → `apple.yaml` 显式定义
- **www.clarity.ms**：微软 Clarity 遥测长期 timeout 污染日志 → `custom_reject`

## 📝 维护

- 手写规则集：直接 `git commit && git push`
- Mirror 规则集：**别手改**，由 Actions 自动覆盖
- Clash 端按 `interval` 自动拉取（默认建议 3600 秒）
