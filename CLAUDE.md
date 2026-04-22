# DailyRules 项目文档

## 项目概述

DailyRules 是一个**规则集仓库**，用于代理工具（sing-box、Clash 等）的分流规则管理。

**核心功能**：分类管理日常分流规则，自动编译发布，支持多客户端引用。

## 目录结构

```
DailyRules/
├── src/                         # 规则源文件
│   ├── game/                   # 游戏规则 (直连)
│   │   ├── kg-mc.json          # 库洛/鸣潮
│   │   └── steam-direct.json   # Steam 下载
│   ├── *.json                  # 其他规则 (代理/直连)
│   └── dist/                   # 编译输出 (自动生成)
├── .github/workflows/
│   └── compile.yml             # CI 编译发布
└── README.md
```

## 规则文件格式

### v2 格式 (sing-box)
```json
{
  "version": 2,
  "rules": [
    {
      "domain": ["example.com"],
      "outbound": "direct"
    },
    {
      "geosite": ["google"],
      "outbound": "proxy"
    }
  ]
}
```

### v1 格式 (兼容)
```json
{
  "version": 1,
  "rules": [
    {
      "domain_suffix": ["example.com"]
    }
  ]
}
```

## 规则分类

| 类型 | 说明 | 示例 |
|------|------|------|
| proxy | 代理规则 | google, youtube, github |
| direct | 直连规则 | cn, bilibili, 国内域名 |
| block | 广告拦截 | category-ads-all |

## 添加新规则

### 方式 1: 手动创建

在 `src/` 下创建 JSON 文件：
```bash
# 创建代理规则
echo '{"version":2,"rules":[{"domain_suffix":["newsite.com"],"outbound":"proxy"}]}' > src/newrule.json

# 创建直连规则
echo '{"version":2,"rules":[{"domain_suffix":["cn-site.com"],"outbound":"direct"}]}' > src/cn-rule.json

# 创建游戏规则
echo '{"version":1,"rules":[{"domain_suffix":["game.com"]}]}' > src/game/game-new.json
```

### 方式 2: 从 MetaCubeX 下载

```bash
# 代理规则
curl -sL "https://ghfast.top/https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/xxx.json" -o src/xxx.json

# 直连规则
curl -sL "https://ghfast.top/https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/meta/geo/geosite/xxx.json" -o src/xxx.json

# geoip 规则
curl -sL "https://ghfast.top/https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geoip/xxx.json" -o src/xxx.json
```

## 发布流程

### 手动发布

```bash
# 1. 提交更改
git add src/
git commit -m "feat: add new rule"

# 2. 推送到 GitHub (触发 CI)
git push

# 3. 或打标签发布正式版本
git tag v1.0.0
git push origin main --tags
```

### CI 自动发布

推送到 main 分支 → 自动编译 SRS → 创建 nightly Release
打 v* 标签 → 创建正式 Release

## 引用规则

### sing-box 配置示例

```json
{
  "route": {
    "rule_set": [
      {
        "tag": "google",
        "type": "remote",
        "format": "binary",
        "url": "https://github.com/TextlineX/DailyRules/releases/download/nightly/google.srs",
        "download_detour": "proxy"
      },
      {
        "tag": "cn",
        "type": "remote",
        "format": "binary",
        "url": "https://github.com/TextlineX/DailyRules/releases/download/nightly/cn.srs"
      }
    ]
  }
}
```

### JSON 源码格式 (其他客户端)

```json
{
  "tag": "google",
  "type": "remote",
  "format": "source",
  "url": "https://raw.githubusercontent.com/TextlineX/DailyRules/main/src/google.json"
}
```

## CDN 加速

规则文件通过以下 CDN 加速：

| CDN | URL 格式 | 适用 |
|-----|---------|------|
| 官方源 | `https://raw.githubusercontent.com` | 有代理 |
| ghfast.top | `https://ghfast.top/https://raw.githubusercontent.com` | 国内直连 |
| ghproxy.com | `https://ghproxy.com/https://raw.githubusercontent.com` | 国内直连 |

## 与 ConfigGen 联动

ConfigGen 会自动引用 DailyRules 的规则：

```
ConfigGen/src/config.json (节点)
         ↓
    读取 DailyRules/src/*.json
         ↓
    生成 singbox/clash/surge 配置
```

ConfigGen 中的规则 URL 配置在 `generators/singbox.js` 的 `ruleSetSources` 对象中。

## 常见问题

### Q: 如何添加自定义规则？
A: 在 `src/` 或 `src/game/` 下创建 JSON 文件，遵循 v1/v2 格式。

### Q: 如何删除某个规则？
A: 删除对应的 JSON 文件，CI 会自动重新编译发布。

### Q: 规则下载失败怎么办？
A: 检查 CDN 是否可用，更新 URL 中的 CDN 地址。

### Q: 如何修改现有规则？
A: 直接编辑 `src/` 下的 JSON 文件，提交后 CI 自动更新。

## MetaCubeX 规则库

规则来源: https://github.com/MetaCubeX/meta-rules-dat

### 常用路径

```
sing/geo/geosite/     # 代理类 geosite 规则
meta/geo/geosite/     # 直连类 geosite 规则
sing/geo-lite/geosite/# 精简版规则
sing/geo/geoip/       # IP 规则
meta/geo/geoip/       # IP 规则 (meta)
```

### 常用规则

| 规则名 | 路径 | 说明 |
|--------|------|------|
| google | sing/geo/geosite/google.json | Google 全家桶 |
| youtube | sing/geo/geosite/youtube.json | YouTube |
| telegram | sing/geo/geosite/telegram.json | Telegram |
| github | sing/geo/geosite/github.json | GitHub |
| netflix | sing/geo/geosite/netflix.json | Netflix |
| cn | sing/geo/geosite/cn.json | 中国域名 |
| bilibili | meta/geo/geosite/bilibili.json | 哔哩哔哩 |
| category-ads-all | meta/geo/geosite/category-ads-all.json | 广告拦截 |
