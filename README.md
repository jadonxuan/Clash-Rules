# Clash-Rules

个人 Clash / OpenClash 规则集，托管在 GitHub，通过 `rule-providers` 订阅。

用于 iStoreOS 旁路由（192.168.77.100）上的 OpenClash。

## 📁 结构

```
rules/
├── custom_proxy.yaml    # ⭐ 强制走代理：伪装成 .cn 的境外服务（如 tnjxaon.cn/onblix.com）
├── custom_direct.yaml   # ⭐ 强制直连：本地/内网/自定义直连域
├── custom_reject.yaml   # ⭐ 广告/遥测拒绝（clarity.ms 等）
├── apple_fix.yaml       # 修复 token.safebrowsing.apple 之类 DNS 解析失败
├── ai.yaml              # AI 服务（ChatGPT/Claude/Gemini/Copilot）
ip/
└── private_ip.yaml      # 私有网段直连
examples/
└── openclash-integration.yaml  # 集成到 LSDL.yaml 的示例片段
```

## 🔗 订阅 URL

**GitHub 原始**：
```
https://raw.githubusercontent.com/jadonxuan/Clash-Rules/main/rules/<file>.yaml
```

**国内加速（择一）**：
```
https://ghproxy.com/https://raw.githubusercontent.com/jadonxuan/Clash-Rules/main/rules/<file>.yaml
https://cdn.jsdelivr.net/gh/jadonxuan/Clash-Rules@main/rules/<file>.yaml
```

## 🧭 优先级设计

在 OpenClash 的 `rules:` 列表里，**顺序 = 优先级**。建议自上而下：

1. `RULE-SET,custom_reject,REJECT`         ← 先拒绝广告
2. `RULE-SET,custom_direct,DIRECT`         ← 再强制直连
3. `RULE-SET,custom_proxy,🚀 默认代理`     ← 再强制代理（覆盖 cn_domain 误判）
4. `RULE-SET,apple_fix,DIRECT`             ← 修复 apple
5. `RULE-SET,ai,🤖 ChatGPT`
6. ... 第三方规则集（tiktok / google / apple / cn_domain / geolocation-!cn 等）
7. `MATCH,🐟 漏网之鱼`                     ← 兜底

## 🩺 已解决的问题

- **hxaws.tnjxaon.cn / hkapp.tnjxaon.cn / cha.onblix.com**：伪装 .cn 的境外代理 App 服务端，原走 `cn_domain` 直连必超时 → 移入 `custom_proxy` 强制走代理
- **token.safebrowsing.apple**：DNS 解析失败 → `apple_fix` 中显式定义走直连
- **www.clarity.ms**：微软 Clarity 遥测，长期 timeout 污染日志 → `custom_reject`

## 📝 更新

改完文件 `git commit && git push` 即可，Clash 端按 `interval` 自动拉取（默认建议 3600 秒）。
