# DailyRules - 日常规则集

规则分为三类：**proxy**（代理）、**direct**（直连）、**block**（广告拦截）。

## 规则分类

| 文件 | 类型 | 说明 |
|------|------|------|
| `google.json` / `.srs` | proxy | Google 全家桶 |
| `youtube.json` / `.srs` | proxy | YouTube |
| `telegram.json` / `.srs` | proxy | Telegram |
| `github.json` / `.srs` | proxy | GitHub |
| `netflix.json` / `.srs` | proxy | Netflix |
| `disney.json` / `.srs` | proxy | Disney+ |
| `steam.json` / `.srs` | proxy | Steam |
| `category-ai-!cn.json` / `.srs` | proxy | AI 服务（非中国） |
| `geolocation-!cn.json` / `.srs` | proxy | 非中国地区 |
| `cn.json` / `.srs` | direct | 中国域名 |
| `bilibili.json` / `.srs` | direct | 哔哩哔哩 |
| `category-ads-all.json` / `.srs` | block | 广告拦截 |
| `dreista-ads.json` / `.srs` | block | Dreista 广告规则 |

## 引用方式

### SRS（二进制，推荐）

```json
{
  "tag": "google",
  "type": "remote",
  "format": "binary",
  "url": "https://github.com/TextlineX/DailyRules/releases/download/nightly/google.srs"
}
```

### JSON（源码，可直接编辑）

```json
{
  "tag": "google",
  "type": "remote",
  "format": "source",
  "url": "https://raw.githubusercontent.com/TextlineX/DailyRules/main/src/google.json"
}
```

## 发布方式

推送 `v*` 标签创建正式 Release，直接 push 到 main 分支创建 nightly 构建。

```bash
git add .
git commit -m "feat: update rules"
git tag v1.0.0
git push origin main --tags
```