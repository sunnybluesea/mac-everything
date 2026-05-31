# mac-everything

macOS 版 Everything 文件搜索——SQLite FTS5 即时文件名搜索。

## 架构

```
mac-everything (CLI引擎)  →  mac-everything-gui (HTTP服务 :8765)  →  浏览器前端
                                     ↑
                          launchd 开机自启 (com.zhl.mac-everything-server)
```

## 安装
可直接在右边releases下载mac-everything.dmg，或复制以下地址
(https://github.com/user-attachments/files/28434642/mac-everything.dmg.zip)

# 1. 拷贝脚本
cp mac-everything mac-everything-gui ~/.local/bin/
chmod +x ~/.local/bin/mac-everything*

# 2. 首次构建索引
mac-everything index

# 3. 安装 launchd 服务（开机自启）
cp com.zhl.mac-everything-server.plist ~/Library/LaunchAgents/
launchctl load ~/Library/LaunchAgents/com.zhl.mac-everything-server.plist

# 4. 打开搜索页面
open http://127.0.0.1:8765
# 或双击 mac-everything.webloc
```

## 命令

| 命令 | 说明 |
|------|------|
| `mac-everything search "xxx"` | 搜索文件 |
| `mac-everything search "*.pdf"` | 搜索 PDF |
| `mac-everything index` | 重建索引 |
| `mac-everything update` | 增量同步 |
| `mac-everything stats` | 索引状态 |

## 技术栈

- Python 3 + SQLite FTS5
- 无外部依赖（GUI 仅用 http.server）
- 40 万文件搜索 < 40ms
- 增量同步 < 10s

## Author

**sunnybluesea**
