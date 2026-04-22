# DailyRules - 日常规则集

基于 MetaCubeX/meta-rules-dat 规则库，分类管理日常分流规则。

## 目录结构

```
DailyRules/
├── src/
│   ├── game/                    # 游戏规则
│   │   ├── kg-mc.json          # 库洛/鸣潮
│   │   └── steam-direct.json   # Steam 下载直连
│   ├── *.json                  # 代理规则 (google, youtube, github...)
│   ├── cn.json                  # 中国域名直连
│   └── bilibili.json            # 哔哩哔哩
├── dist/                        # 编译输出 (SRS 二进制)
├── .github/workflows/          # CI 自动编译发布
└── README.md
```

## 规则分类

### 代理规则 (默认走代理)
| 文件 | 说明 |
|------|------|
| `google.json` | Google 全家桶 |
| `youtube.json` | YouTube |
| `telegram.json` | Telegram |
| `github.json` | GitHub |
| `netflix.json` / `disney.json` | 流媒体 |
| `steam.json` | Steam 代理 |
| `category-ai-!cn.json` | AI 服务 |
| `geolocation-!cn.json` | 非中国 |
| ... | |

### 直连规则 (默认走直连)
| 文件 | 说明 |
|------|------|
| `cn.json` | 中国域名 |
| `bilibili.json` | 哔哩哔哩 |
| `google-meta.json` | Google 中国 |
| `microsoft.json` | 微软中国 |
| ... | |

### 游戏规则
| 文件 | 说明 |
|------|------|
| `game/kg-mc.json` | 库洛/鸣潮直连 |
| `game/steam-direct.json` | Steam 下载直连 |

### 广告拦截
| 文件 | 说明 |
|------|------|
| `category-ads-all.json` | 广告拦截 |

## 发布格式

每个 JSON 文件同时发布两种格式：
- **JSON** (`*.json`) - 源码，可直接编辑
- **SRS** (`*.srs`) - 二进制，高性能

## 引用方式

### SRS (推荐)
```json
{
  "tag": "google",
  "type": "remote",
  "format": "binary",
  "url": "https://github.com/TextlineX/DailyRules/releases/download/nightly/google.srs"
}
```

### JSON (源码)
```json
{
  "tag": "google",
  "type": "remote",
  "format": "source",
  "url": "https://raw.githubusercontent.com/TextlineX/DailyRules/main/src/google.json"
}
```

## 手动编译

```bash
# 安装 sing-box
brew install sing-box  # macOS
# 或从 https://github.com/SagerNet/sing-box 下载

# 编译单个规则
sing-box rule-set compile src/google.json -o dist/google.srs

# 编译全部规则
for f in src/*.json; do
  sing-box rule-set compile "$f" -o "dist/$(basename $f .json).srs"
done
```

## 发布流程

1. 修改 `src/` 下的规则文件
2. 推送到 GitHub
3. GitHub Actions 自动编译并发布

```bash
git add .
git commit -m "update rules"
git push
# 或打标签发布正式版本
git tag v1.0.0
git push origin main --tags
```

## CDN 加速

ConfigGen 使用以下 CDN：
- 官方源: `https://raw.githubusercontent.com`
- ghfast.top: `https://ghfast.top/https://raw.githubusercontent.com`
- ghproxy.com: `https://ghproxy.com/https://raw.githubusercontent.com`

## 规则来源

- MetaCubeX/meta-rules-dat (sing/geo/)
- MetaCubeX/meta-rules-dat (meta/geo/)
- 用户自定义规则
