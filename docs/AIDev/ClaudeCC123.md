---
coding: UTF-8
title: ClaudeCC123
date: 2026-07-12T20:44:10
updated: 2026-07-18T03:33:49
tags: []
share: true
---

# ClaudeCC

## 1. Claude Desktop

### 1.1 应用程序本体

```
/Applications/Claude.app# 应用主体（748 MB）
/opt/homebrew/Caskroom/claude/1.18286.0,.../# Homebrew cask 数据
```

### 1.2 用户数据（配置、会话、VM 等）

```
~/Library/Application Support/Claude/          # 1P 模式数据（292 MB）
~/Library/Application Support/Claude-3p/       # 3P 模式数据（6.9 GB）
├── claude_desktop_config.json                # 主配置
├── vm_bundles/                                # VM 镜像
├── local-agent-mode-sessions/                 # 会话数据
├── IndexedDB/                                 # 聊天历史
└── ...
```

### 1.3 缓存和日志

```
~/Library/Caches/com.anthropic.claude/         # 缓存
~/Library/Logs/Claude/                         # 日志
~/Library/Preferences/com.anthropic.claude.plist  # 偏好设置
~/Library/WebKit/com.anthropic.claude/         # WebKit 数据
```

### 1.4 核心配置文件

```
claude_desktop_config.json
```
- **作用：** Claude 桌面版的主配置文件，最重要的配置之一。
- **包含内容：**
	- MCP (Model Context Protocol) 服务器配置
	- 自定义工具和插件
	- 快捷键设置
	- 工作目录偏好

- **例子：** 如果你配置了文件系统访问或其他 MCP 服务器，配置就存在这里：
	```
	{
	  "mcpServers": {
	    "filesystem": {
	      "command": "npx",
	      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed"]
	    }
	  }
	}
	```
- **能否清理：** 删了会丢失所有自定义配置

```
config.json
```
- **作用：** 运行时配置，记录应用的当前状态和运行参数。

- **包含内容：**
	- 当前选择的模型
	- UI 设置
	- 功能开关状态

- **能否清理：** 删了可能导致应用设置重置

```
Preferences
```
- **作用：** 用户偏好设置的持久化存储。
- **包含内容：**
	- 界面主题
	- 字体大小
	- 通知设置
	- 是否启用某些功能

- **能否清理：** 删了会重置为默认设置

### 1.5 会话和数据存储

```

local-agent-mode-sessions/
```
- **作用：** **这是最占空间的地方之一！** 存储所有 Cowork 和 Code 会话的完整数据。

- **结构示例：**
	```
	local-agent-mode-sessions/
	├── e2f14fad/               # 项目ID
	│   └── 00000000/           
	│       ├── local_xxxxx/    # 具体会话ID
	│       │   ├── uploads/    # 你上传的文件
	│       │   ├── outputs/    # 生成的文件
	│       │   └── mnt/        # 挂载的工作文件夹
	│       └── local_yyyyy/    # 另一个会话
	└── f3e25gbe/               # 另一个项目
	```

- **例子：**
	- 你上传了一个 10MB 的 Excel → 存在某个会话的 uploads/
	- Claude 帮你生成了文档 → 存在 outputs/
	- 你选择了一个工作文件夹 → 链接信息在 mnt/

- **能否清理：** 可以删除旧的会话文件夹，不影响新会话

- **清理方法：**
	```
	cd ~/Library/Application\ Support/Claude-3p/local-agent-mode-sessions/`
	# 查看各会话大小
	du -sh */
	# 删除某个旧项目的所有会话
	rm -rf e2f14fad/
	```

```

IndexedDB/
```
- **作用：** 浏览器风格的数据库，存储结构化数据。
- **包含内容：**
	- 聊天历史记录
	- 对话元数据
	- 项目信息
	- Artifacts 数据
- **例子：** 你在 Cowork 里的所有对话记录、生成的 React 组件预览等都存在这里。
- **能否清理：** 可以删，但会丢失所有聊天历史和项目记录

```
blob_storage/
```
- **作用：** 存储二进制大对象（Binary Large Objects）。
- **包含内容：**
	- 上传的图片
	- PDF 文件
	- 其他附件

- **例子：** 你上传截图让 Claude 分析，这些图片就存在这里。

- **能否清理：** 删了会丢失上传的附件引用

```
Session Storage/
```
- **作用：** 临时会话存储，类似浏览器的 sessionStorage。
- **包含内容：**
	- 当前会话的临时状态
	- 未保存的草稿
	- UI 临时数据
- **能否清理：** 关闭应用后可以清理

### 1.6 VM 和代码执行

```
vm_bundles/
```
- **作用：** **第二大空间占用！** 存储 Cowork 和 Code 功能所需的虚拟机镜像。

- **包含内容：**
	- Linux VM 镜像（Ubuntu 22）
	- Python、Node.js 等运行时环境
	- 预装的常用工具
- **大小：** 通常 5-6 GB
- **例子：** 当你让 Claude 运行 Python 脚本、安装 npm 包时，都在这个 VM 里执行。
- **能否清理：** 可以删，但下次打开 Cowork/Code 时会重新下载（需要联网 + 时间）

- **检查大小：**`du -sh ~/Library/Application\ Support/Claude-3p/vm_bundles/`
```
claude-code-vm/
```
- **作用：** VM 的运行时数据和状态。
- **包含内容：**
	- VM 当前运行状态
	- 临时文件系统数据
	- 网络配置
- **能否清理：** 建议不删，删了可能导致 VM 启动失败
```
git-worktrees.json
```
- **作用：** 记录 Git worktree 的使用情况。
- **包含内容：**
	- 当前激活的 worktree
	- worktree 路径映射
- **例子：** 当你让 Claude 在隔离的 Git worktree 中工作时，信息记录在这里。
- **能否清理：** 如果不用 worktree 功能可以删

### 1.7 缓存文件

```
Cache/
Code Cache/
GPUCache/
```
- **作用：** 各种缓存数据，加速应用运行。
- **包含内容：**
	- 下载的资源缓存
	- 编译后的代码
	- 渲染缓存
- **例子：** 应用图标、字体、网页资源等。
- **能否清理：** 完全安全，删了会自动重建
- **清理命令：**
	```
	cd ~/Library/Application\ Support/Claude-3p/
	rm -rf Cache/ "Code Cache/" GPUCache/
	```

```
DawnGraphiteCache/** **DawnWebGPUCache/
```
- **作用：** GPU 相关的图形缓存。
- **包含内容：**
	- GPU shader 编译缓存
	- WebGPU 渲染数据
- **能否清理：** 完全安全
```
fcache
```
- **作用：** 文件缓存索引。
- **能否清理：** 安全清理

### 1.8 网络和安全

```
Cookies
Cookies-journal
```
- **作用：** 存储登录凭证和会话 Cookie。
- **包含内容：**
	- Anthropic 账户登录 token
	- 会话认证信息
- **例子：** 你登录 Claude 桌面版后，登录状态就存在这里。
- **能否清理：** 删了需要重新登录

```
host-creds-\*.json
```
- **作用：** 主机凭证文件。
- **包含内容：**
	- 与 Anthropic 服务器通信的凭证
	- 设备认证信息
- **能否清理：** 删了可能无法连接服务器

```
TransportSecurity
```
- **作用：** HTTPS 安全传输状态。
- **包含内容：**
	- HSTS (HTTP Strict Transport Security) 策略
	- 证书验证缓存

- **能否清理：** 建议不删
```
Network Persistent State
```
- **作用：** 网络层持久状态。
- **包含内容：**
	- DNS 缓存
	- 代理设置
	- 网络质量监测数据

- **能否清理：** 建议不删

### 1.9 调试和错误报告

```
Crashpad/
```
- **作用：** 崩溃报告收集。
- **包含内容：**
	- 应用崩溃时的内存转储
	- 堆栈跟踪信息
- **能否清理：** 完全安全，除非你在调试问题
```
sentry/
```
- **作用：** Sentry 错误追踪服务的本地数据。
- **包含内容：**
	- 错误日志
	- 性能监控数据
**能否清理：** 完全安全

### 1.10 其他

```
window-state.json
```
- **作用：** 记住窗口的位置和大小。

- **包含内容：**
	```
	{
	  "x": 100,
	  "y": 200,
	  "width": 1200,
	  "height": 800,
	  "isMaximized": false
	}
	```
- **能否清理：** 删了窗口会重置到默认位置和大小
```
title-gen/
```
- **作用：** 对话标题自动生成的缓存。
- **例子：** 你开始一个新对话，Claude 自动给它起名"Excel 公式计算问题"，这个标题生成过程的缓存。
- **能否清理：** 安全清理
```
*pending-uploads/
```
- **作用：** 临时存储等待上传的文件。

- **例子：** 你拖拽一个大文件到聊天框，还没发送时暂存在这里。

- **能否清理：** 通常可以清理

## 2. Claude Desktop 清理

### 2.1 安全清理

```bash

## 系统缓存
rm -rf ~/Library/Caches/com.anthropic.claudefordesktop/
rm -rf ~/Library/Caches/com.anthropic.claudefordesktop.ShipIt/
rm -rf ~/Library/WebKit/com.anthropic.claude/

## 日志
rm -rf ~/Library/Logs/Claude/
rm -rf ~/Library/Logs/Claude-3p/

## 3P 模式内的缓存
cd ~/Library/Application\ Support/Claude-3p/
rm -rf Cache/
rm -rf "Code Cache/"
rm -rf GPUCache/
rm -rf DawnGraphiteCache/
rm -rf DawnWebGPUCache/
rm -rf "Shared Dictionary/"
rm -rf Crashpad/
rm -rf sentry/
rm -rf title-gen/
rm -rf pending-uploads/
rm -f fcache
rm -f .DS_Store
```

### 2.2 酌情清理

```bash
## 2.2.1 旧的会话数据（先查看大小）
du -sh ~/Library/Application\ Support/Claude-3p/local-agent-mode-sessions/e2f14fad/00000000/local_*
## 删除具体会话（成对删除）
rm -rf ~/Library/Application\ Support/Claude-3p/local-agent-mode-sessions/e2f14fad/00000000/local_11849f6d-2d50-4eae-bdc7-df0e5003fcd3/
rm ~/Library/Application\ Support/Claude-3p/local-agent-mode-sessions/e2f14fad/00000000/local_11849f6d-2d50-4eae-bdc7-df0e5003fcd3.json

## 2.2.2 聊天历史数据库
rm -rf ~/Library/Application\ Support/Claude-3p/IndexedDB/
rm -rf ~/Library/Application\ Support/Claude-3p/Local\ Storage/
rm -rf ~/Library/Application\ Support/Claude-3p/WebStorage/
rm -f ~/Library/Application\ Support/Claude-3p/SharedStorage
rm -f ~/Library/Application\ Support/Claude-3p/SharedStorage-wal

## 2.2.3 会话状态
rm -rf ~/Library/Application\ Support/Claude-3p/Session\ Storage/

## 2.2.4 窗口位置
rm -f ~/Library/Application\ Support/Claude-3p/window-state.json

## 2.2.5 崩溃恢复相关
rm -f ~/Library/Application\ Support/Claude-3p/DIPS
rm -f ~/Library/Application\ Support/Claude-3p/DIPS-wal
```

###  2.3 不要删除

```bash
# VM 镜像
~/Library/Application Support/Claude-3p/vm_bundles/
# VM 状态
~/Library/Application Support/Claude-3p/claude-code-vm/
~/Library/Application Support/Claude-3p/claude-code/
# Cookies（登录状态）
~/Library/Application Support/Claude-3p/Cookies
~/Library/Application Support/Claude-3p/Cookies-journal
# 分区数据
~/Library/Application Support/Claude-3p/Partitions/

claude_desktop_config.json     # 主配置文件（MCP 服务器等）
config.json                    # 运行时配置
Local State                    # 应用状态
Preferences                    # 用户偏好
ant-did                        # 设备 ID
host-creds-*.json             # 服务器凭证
TransportSecurity              # HTTPS 安全策略
Trust Tokens / Trust Tokens-journal  # 信任令牌
Network Persistent State       # 网络状态
cowork-enabled-cli-ops.json    # 功能开关
git-worktrees.json             # Git worktree 配置
configLibrary/                 # 配置库
~/Library/Preferences/com.anthropic.claude.plist  # 系统偏好
```

## 3. Claude Code CLI

### 3.1 应用程序本体

```
/opt/homebrew/Caskroom/claude-code/2.1.169/    # 二进制文件（221.9 MB）
├── claude                                     # 可执行文件
└── ...
/opt/homebrew/bin/claude                       # 符号链接（指向上面）
```

### 3.2 用户数据

```
~/.claude/
├── settings.json                    # 全局配置文件
├── projects/                        # 项目会话记录 ⚠️ 最占空间
├── todos/                           # 任务列表
├── statsig/                         # 遥测数据
├── shell-snapshots/                 # Shell 状态快照
├── history.jsonl                    # 全局命令历史
├── file-history/                    # 文件操作历史
├── ide/                             # IDE 集成数据
├── plugins/                         # 已安装插件
├── skills/                          # 用户自定义技能
├── commands/                        # 自定义斜杠命令
├── agents/                          # 自定义子代理
├── output-styles/                   # 输出样式配置
├── CLAUDE.md                        # 全局记忆文件
└── memory/                          # 记忆数据
```

### 3.3 文件详解

#### `settings.json`

- **作用：** 全局设置文件
- **包含：** API 配置、模型偏好、快捷键、UI 设置等
- **大小：** 通常 < 10 KB
- **能删吗：** 删了配置全没

**例子内容：**

```json
{
  "model": "claude-sonnet-4",
  "theme": "dark",
  "editorCommand": "code",
  "defaultAgent": "general-purpose"
}
```

#### `projects/`

- **作用：** 每个项目的**完整会话历史**
- **结构：**

```
projects/
├── -Users-yanran-yrLs-Agent-tmp/          # 项目路径的转义
│   ├── <会话ID-1>.jsonl                   # 一次对话的完整消息
│   ├── <会话ID-2>.jsonl
│   └── ...
├── -Users-yanran-Projects-myapp/
│   └── ...
└── ...
```

**`.jsonl` 文件是什么：**

- 每一行是一条消息（JSON Lines 格式）
- 包含用户输入、AI 回复、工具调用记录
- **每次跟 Claude Code 对话都会累积**

**大小估算：**

- 短对话：几十 KB
- 长对话（含代码）：几百 KB - 几 MB
- 一个项目用了几个月：**可能几百 MB - 几 GB**

**能否清理：** 可以删除，但会丢失对话历史

**查看各项目占用：**

```bash
du -sh ~/.claude/projects/*/ | sort -h
```

#### `todos/`

- **作用：** 存储 TaskCreate 创建的任务列表
- **结构：**

```
todos/
├── <会话ID>-agent-<主代理ID>.json
├── <会话ID>-agent-<子代理ID>.json
└── ...
```

- **大小：** 通常几 KB - 几十 KB
- **能删吗：** 安全清理（会丢失历史任务列表）

#### `statsig/`

- **作用：** Anthropic 的功能开关和 A/B 测试遥测
- **结构：**

```
statsig/
├── statsig.stable_id.<hash>
├── statsig.cached.evaluations.<hash>
└── statsig.session_id.<hash>
```

- **大小：** 通常 < 1 MB
- **能删吗：** 完全安全，会自动重建

#### `shell-snapshots/`

- **作用：** 保存 Shell 环境的快照（PATH、别名、函数等）
- **用途：** 让 Claude Code 执行 Bash 命令时用你的真实环境
- **大小：** 通常几 MB - 几十 MB
- **能删吗：** 可以清理（会重新生成）

**清理示例：**

```bash
ls -lah ~/.claude/shell-snapshots/
# 会看到很多类似 snapshot-bash-xxxxx.sh 的文件
```

#### `history.jsonl`

- **作用：** 全局的用户输入历史（跨项目）
- **用途：** 输入命令时的上箭头历史
- **大小：** 通常 < 10 MB
- **能删吗：** 安全（会丢失命令历史）

#### `file-history/`

- **作用：** Claude Code 修改文件的历史备份
- **用途：** 可以撤销 Claude 对文件的修改
- **大小：** 视修改文件量而定，**可能几百 MB**
- **能删吗：** 可以清理，但会丢失撤销能力

#### `ide/`

- **作用：** VS Code 等 IDE 集成的数据
- **包含：** IDE 端口锁、临时通信数据
- **大小：** 很小（几 KB）
- **能删吗：** 使用中不要删

#### `plugins/`

- **作用：** 用户安装的插件
- **能删吗：** 删了会丢失已安装插件

#### `skills/`, `commands/`, `agents/`, `output-styles/`

- **作用：** 用户自定义的技能、斜杠命令、子代理、输出样式
- **能删吗：** 删了会丢失自定义内容

#### `CLAUDE.md`

- **作用：** **全局记忆文件**（跨所有项目的记忆）
- **包含：** 你想让 Claude 一直记住的偏好、规则等
- **能删吗：** 会丢失全局记忆

#### `memory/`

- **作用：** 结构化的记忆存储
- **能删吗：** 会丢失记忆数据

## 4. Claude code清理

### 4.1 完全安全清理

- 不影响任何功能，自动重建

```bash
# === 缓存 ===
rm -rf ~/.claude/statsig/
rm -rf ~/.claude/shell-snapshots/
rm -rf ~/.claude/logs/
rm ~/.claude/history.jsonl 2>/dev/null
rm -rf ~/Library/Caches/claude-cli-nodejs/
rm -rf ~/.claude/cache/ # 缓存
rm -rf ~/.claude/telemetry/ # 遥测数据

# === 临时文件 ===
find /tmp -maxdepth 2 -iname "*claude*" -user $(whoami) -exec rm -rf {} \; 2>/dev/null

# === XDG 标准目录（如果有） ===
rm -rf ~/.cache/claude/ 2>/dev/null             # XDG 缓存
rm -rf ~/.local/state/claude/ 2>/dev/null       # XDG 状态文件

# === 应用状态 ===
rm -rf ~/Library/Saved\ Application\ State/com.anthropic.claude-code.savedState/ 2>/dev/null
```

### 4.2 有取舍的清理

- 会丢失历史记录，但不影响功能

```bash
# === 1. 查看各项目对话历史占用（重要！先看再决定删什么） ===
du -sh ~/.claude/projects/*/ 2>/dev/null | sort -hr | head -20

# === 2. 删除特定旧项目的对话历史 ===
rm -rf ~/.claude/projects/<某个项目文件夹>/

# === 3. 删除所有项目的对话历史（最占空间！） ===
rm -rf ~/.claude/projects/

# === 4. 删除文件修改历史（丢失撤销能力） ===
rm -rf ~/.claude/file-history/

# === 5. 删除旧任务列表 ===
rm -rf ~/.claude/todos/

# === 6. IDE 集成数据 ===
rm -rf ~/.claude/ide/

# === 7. 输出样式配置 ===
rm -rf ~/.claude/output-styles/

# === 8. 会话状态（如果有） ===
rm -rf ~/.claude/sessions/ 2>/dev/null

# === 9. 分析数据（如果有） ===
rm -rf ~/.claude/analytics/ 2>/dev/null
rm -rf ~/.local/share/claude/ 2>/dev/null

# === 10. 配置文件的自动备份 ===
rm -rf ~/.claude/backups/

# === 11. 会话的环境变量快照 ===
rm -rf ~/.claude/session-env/

# === 删除用户自定义内容 ===
rm -rf ~/.claude/skills/          # 自定义技能
rm -rf ~/.claude/commands/        # 自定义斜杠命令
rm -rf ~/.claude/agents/          # 自定义子代理
rm -rf ~/.claude/plugins/         # 已安装插件
rm ~/.claude/CLAUDE.md            # 全局记忆文件
rm -rf ~/.claude/memory/          # 结构化记忆数据（比 CLAUDE.md 更详细的记忆系统）
rm ~/.claude/settings.json
rm ~/.claude.json                 # API key、默认模型、权限设置、主题等
rm -rf ~/.config/claude           # XDG 配置
```
