# mac-everything v2.0

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

```bash
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
- 70 万文件搜索 < 100ms
- 增量同步 < 60s
- 后台每小时自动同步

## v2.0 新功能

- **CJK 中日韩文字搜索**：汉字/日文/韩文支持任意子串搜索，按字符级分词
- **多关键词 AND 搜索**：空格分隔多个关键词，取交集，如 `派格兰 ppt`
- **隐藏文件开关**：右上角开关，默认不显示路径中含 `.` 开头文件夹（如 `.git` `.npm`）的文件
- **数据库更新时间**：标题栏括号内显示最后索引时间，精确到分钟
- **Web 图形界面**：浏览器访问 `http://127.0.0.1:8765`，支持分类筛选（全部/文档/图片/代码/文件夹）
- **全局文件系统扫描**：索引范围 `/` 而非仅 `/Users`
- **后台自动同步**：每小时增量更新索引
- **结果按日期排序**：最新修改的文件在最前面
- **键盘快捷键**：`⌘K` 聚焦搜索框，`↑↓` 导航结果，`Enter` 打开，`⌘Enter` 在 Finder 中定位

## v2.0 修复的 Bug

| 问题 | 修复 |
|------|------|
| 文件搜索不全 | 扫描路径从 `/Users` 扩展为 `/`，新增排除系统噪音路径 |
| 新文件搜不到 | 新增每小时后台增量同步 + 启动时自动同步 |
| 多关键词不生效 | FTS5 查询改为 `AND` 连接，每个 term 前缀匹配 |
| 中文搜不到 | Unicode 字符级分词，覆盖 CJK 全部 Extension + 假名 + 谚文 |
| 数据库锁定崩溃 | 启用 `busy_timeout=30s`，消除启动时 launchd 竞态 |
| GLOB 路径注入 | 改用 `LIKE ... ESCAPE '\'`，路径含 `[` `]` `*` `?` 也安全 |
| 隐藏文件占满结果 | 服务端过滤隐藏文件，500 条结果全部可见 |

## Author

**sunnybluesea**
