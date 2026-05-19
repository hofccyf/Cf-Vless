# 🌐 ProxyIP Workshop

> **一个纯前端、零依赖的 VLESS 链接生成器 / 订阅工坊**
> 配合 [hofccyf/Cloudflare-Vless](https://github.com/hofccyf/Cloudflare-Vless) Worker 脚本使用 ——
> **完全由用户自备 UUID、CF 域名、落地 IP**，本工具仅负责把它们拼成可用的 VLESS 链接并生成订阅。

[![Engine](https://img.shields.io/badge/Engine-hofccyf%2FCloudflare--Vless-orange)](https://github.com/hofccyf/Cloudflare-Vless)
[![Protocol](https://img.shields.io/badge/Protocol-VLESS%20%2B%20WS%20%2B%20TLS-blue)](#)
[![UI](https://img.shields.io/badge/UI-Tailwind%20v4-38bdf8)](#-在线网页工具-indexhtml)
[![Files](https://img.shields.io/badge/Files-single--HTML-success)](./index.html)
[![Privacy](https://img.shields.io/badge/Data-100%25%20Local-2bb24c)](#-隐私与本地存储)

---

## 📌 目录

- [项目简介](#-项目简介)
- [仓库结构](#-仓库结构)
- [使用指南（核心流程）](#-使用指南核心流程)
- [落地节点录入格式](#-落地节点录入格式)
- [快速上手 · 三步部署 Worker](#-快速上手--三步部署-worker)
- [VLESS 链接格式 & 拼装方法](#-vless-链接格式--拼装方法)
- [path 路径写法详解](#-path-路径写法详解)
- [客户端兼容性（Karing / V2RayN / sing-box）](#-客户端兼容性karing--v2rayn--sing-box)
- [在线网页工具 index.html](#-在线网页工具-indexhtml)
- [隐私与本地存储](#-隐私与本地存储)
- [常见问题 FAQ](#-常见问题-faq)
- [致谢 & 相关项目](#-致谢--相关项目)
- [免责声明](#-免责声明)

---

## 💡 项目简介

本仓库是一个**纯前端工具**，**不包含任何节点数据、不分发任何 IP**。
仓库主体是单一文件 [`index.html`](./index.html)（基于 **Tailwind CSS v4** 浏览器运行时），
为使用 [hofccyf/Cloudflare-Vless](https://github.com/hofccyf/Cloudflare-Vless) Worker 的用户提供一个：

- ⚙️ **VLESS 链接拼装器**：输入自己的 UUID + CF 域名 + 落地 IP，实时生成完整 vless:// 链接
- ☑️ **节点多选 + 批量订阅**：一键复制全部链接 / 复制 Base64 订阅 / 下载 `sub.txt`
- 🍎 **Karing / sing-box 兼容开关**：一键切换 `/?ip=` ↔ `/ip=`，解决 path 被强制 URL-encode 的兼容性问题
- 📱 **二维码导出**：手机客户端扫码导入
- 📖 **完整中文教学**：3 步部署、path 三种写法、链接解剖图、5 步拼装手册、FAQ

> ⚠️ **本仓库不存储、不分发任何代理服务，也不提供任何落地 IP。**
> Worker 脚本部署在你自己的 Cloudflare 账号下，落地 IP 由你自备，本工具只是一个本地运行的"链接拼装计算器"。

---

## 📁 仓库结构

```
net/
├── README.md     ← 本文档
└── index.html    ← 单文件 Web 工具（Tailwind v4 + 原生 JS，无任何依赖）
```

就这么简单 —— 浏览器双击 `index.html` 即可使用，也可部署到 Cloudflare Pages / GitHub Pages / 任何静态托管。

---

## 🚀 使用指南（核心流程）

打开 [`index.html`](./index.html) 后，按以下 3 步操作：

### 1️⃣ 填写你自备的 UUID 与 CF 域名

在「**① 个性化配置**」面板填入：

| 字段 | 来源 | 示例（占位） |
|---|---|---|
| **UUID** | 与你部署在 Cloudflare 的 Worker 脚本里的 `CFG.id` 完全一致 | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` |
| **CF 自定义域名 / Worker 域名** | 你绑定到 Worker 的自定义域，或 Workers 默认的 `*.workers.dev` 域名 | `your-worker.example.com` |

> 没有 UUID？终端执行 `python -c "import uuid;print(uuid.uuid4())"`，或访问 [uuidgenerator.net](https://www.uuidgenerator.net/version4) 生成一个。

### 2️⃣ 录入你自备的落地节点

在「**② 落地节点列表**」上方的文本框，每行一条落地节点，支持以下格式：

```text
🇺🇸 美国 | 1.2.3.4:443
🇯🇵 日本 | 5.6.7.8:8443
🇭🇰 香港 = 9.10.11.12:10443
me.example.com 13.14.15.16:443
17.18.19.20:443
[2001:db8::1]:443
# 行首 # 或 // 视为注释
```

| 解析规则 | 说明 |
|---|---|
| `备注名 \| IP:PORT` | 推荐写法，竖线分隔 |
| `备注名 = IP:PORT` | 等号也行 |
| `备注名 IP:PORT` | 空格分隔 |
| `IP:PORT` | 纯地址，备注自动用 IP |
| 端口缺省 | 默认 `443` |
| IPv6 | 用 `[]:port` 包起来 |
| 注释 | 行首 `#` 或 `//` |

点击「**解析 / 更新**」（或直接编辑，会自动防抖解析），节点卡片会立即生成。每张卡片左上角有复选框，可以：

- ✅ 点击复选框 / 工具栏的「全选 / 反选 / 清空选择」勾选要订阅的节点
- ✕ 点右上角 "✕" 单独删除某条节点（同步从文本框移除）

### 3️⃣ 复制链接 / 二维码 / 下载订阅

- 在节点卡片上点 **复制链接** 直接导入客户端
- 点 **二维码** 弹出 QR 码，手机扫码导入
- 滚到「**③ 批量订阅**」，对**已勾选**的节点：
  - 复制全部链接（明文）
  - 复制 Base64 订阅（用于支持订阅 URL 的客户端）
  - 下载 `sub.txt`

> 💾 所有输入（UUID、域名、节点文本、勾选状态、Karing 开关）都自动保存在浏览器 `localStorage`，下次打开**自动恢复**。

---

## 📝 落地节点录入格式

详见上面 [使用指南 - 步骤 2](#2️⃣-录入你自备的落地节点)。补充几点：

- 重复的 `IP:PORT` 会自动去重（以 `ip:port` 为唯一键）
- 备注名可以是任何文字 / Emoji，会作为节点名出现在客户端
- 文本框留空 + 点「清空全部」会清空全部节点和勾选状态（带确认弹窗）

---

## 🛠️ 快速上手 · 三步部署 Worker

本工具假设你已经部署好 Cloudflare-Vless Worker。若还没，按以下步骤：

### Step 1 · 部署 Cloudflare Worker

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/) → **Workers & Pages** → 创建 Worker
2. 把 [`_worker.js`](https://github.com/hofccyf/Cloudflare-Vless/blob/main/_worker.js) 全部内容复制粘贴到编辑器
3. 点击 **Deploy** 部署

### Step 2 · 改 UUID + 默认落地

在脚本顶部 `CFG` 配置块按需修改：

```js
const CFG = {
  id: '<你自备的 UUID>',                     // ← 改成你自己的 UUID
  chunk: 64 * 1024, dnPack: 32 * 1024, dnTail: 512, dnMs: 0,
  upPack: 16 * 1024, upQMax: 256 * 1024, maxED: 8 * 1024,
  concur: 4                                  // ⚠️ Snippets 部署必须改为 1 !!!
};

const 默认备用小可爱地址 = '<你自备的落地 IP / 域名>';   // ← 不写 path 时的兜底落地
```

| 字段 | 含义 | 必改？ |
|---|---|---|
| `CFG.id` | 你的 UUID，等同于其他脚本的 `userID` | ✅ 必改 |
| `默认备用小可爱地址` | 不写 path 时的兜底落地 IP，**完全由你自备** | ⚪ 推荐 |
| `CFG.concur` | 主连接并发数 | ⚠️ **Snippets 部署必须改为 `1`** |

### Step 3 · 绑定自定义域名

Worker 设置 → **Triggers** → **Add Custom Domain** → 输入 `<你的域名>` → DNS 自动配置完成。

✅ 部署完成后，回到本工具顶部「个性化配置」填入相同的 UUID 与域名，节点链接会自动生成。

---

## 🔑 VLESS 链接格式 & 拼装方法

### 📐 模板

```text
vless://{UUID}@{域名}:443?encryption=none&security=tls&sni={域名}&fp=chrome&type=ws&host={域名}&path={编码后的path}#{节点名}
```

### 📦 5 个变量，你只要填 3 个

| 变量 | 说明 | 示例（占位） |
| --- | --- | --- |
| `{UUID}` | 跟 Worker 里 `CFG.id` 一致，一次设定永远不变 | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` |
| `{域名}` | 你的 CF 自定义域 / Worker 域；`@`、`sni=`、`host=` 三处全填它 | `your-worker.example.com` |
| `{编码后的path}` ⭐ | **唯一随节点变化**的部分（落地 `IP:PORT` URL 编码后塞这里） | `%2F%3Fip%3D1.2.3.4%3A443` |
| `{节点名}` | `#` 后面随便写，客户端会显示成节点名 | `🇺🇸 我的美国节点` |
| 其他参数 | `encryption=none&security=tls&fp=chrome&type=ws` 一字不动 | — |

### 🎬 实战拼装（从 0 到 1，占位示例）

```text
A · 拿一行你的落地 IP   →   <你的落地IP>:<端口>
B · 套 /?ip= 前缀        →   /?ip=<你的落地IP>:<端口>
C · URL 编码             →   %2F%3Fip%3D<你的落地IP>%3A<端口>
D · 填进模板 → 完整链接 ✅
```

最终成品（占位示例，请替换尖括号内容）：

```text
vless://<你的UUID>@<你的CF域名>:443?encryption=none&security=tls&sni=<你的CF域名>&fp=chrome&type=ws&host=<你的CF域名>&path=%2F%3Fip%3D<你的落地IP>%3A<端口>#节点备注
```

### 🪄 URL 编码对照表

| 明文 | 编码 |
| :---: | :---: |
| `/` | `%2F` |
| `?` | `%3F` |
| `=` | `%3D` |
| `:` | `%3A` |

> 不会编码？三种懒人办法：① 用 [urlencoder.org](https://www.urlencoder.org/) ② **直接用本仓库的 `index.html`**（最省事，复制的链接已自动编码） ③ 在 V2RayN 里 path 框写明文 `/?ip=IP:PORT`，软件会自动帮你编码。

---

## 🧩 path 路径写法详解

Worker 支持**两种等价前缀**（看你客户端兼容性选一种）：

| 前缀 | 适用客户端 | 说明 |
|---|---|---|
| `/?ip=...` ⭐ | V2RayN / V2RayNG / Xray 内核 | 经典写法，走 query string |
| `/ip=...`   ✨ | **Karing / Stash / Shadowrocket / sing-box 系**（推荐） | 兼容写法，避免 `?` 被强制 URL-encode |

之后三种落地形式两个前缀都通用：

### ① 直连 IP（最常用）

```text
/?ip=<你的落地IP>:<端口>        ← V2RayN 风格
/ip=<你的落地IP>:<端口>         ← Karing 风格（等价）
```

✅ 速度最稳　✅ 本站节点录入框默认就是这种写法
⚠️ 端口可省略（默认 443），但若你的落地端口非 443，**请务必写全**

### ② 直连域名（家宽 / DDNS）

```text
/?ip=<你的落地域名>[:端口]
```

落地是动态域名时使用，Worker 会先解析再连接。适合自建家宽 / 合租 NAT VPS。

### ③ 远程 TXT 订阅（自动地区池）⭐

```text
/?ip=https://raw.githubusercontent.com/<你的GitHub用户名>/<仓库>/<分支>/<地区>.txt
```

指向一份纯文本 IP 列表（每行一个 `IP:PORT`），Worker 会**自动随机挑一个**用。
TXT 更新了，客户端**不用换链接**，IP 自动跟着仓库走。

> ⛔ **不支持** SOCKS5（`/?ip=socks5://...` 写法无效）
> ⛔ **不要**同时写 `/?ip=A&ip=B`，只会取第一个

---

## 🍎 客户端兼容性（Karing / V2RayN / sing-box）

不同客户端的内核处理 WebSocket `path` 的方式不同，**直接影响 path 切换落地能否生效**。

### 🔬 问题现象

> Karing 测试节点能通，但实际上网时无论怎么切换落地 IP，都只会跑 Worker 里 `默认备用小可爱地址` 那个兜底 IP；V2RayN 完全没这个问题。

### 🧠 根本原因

| 客户端 / 内核 | path 在 WS 升级请求中 | 结果 |
|---|---|---|
| **V2RayN / V2RayNG** （Xray 内核） | `GET /?ip=1.2.3.4:443 HTTP/1.1` | ✅ 原样发送，Worker `searchParams.get('ip')` 命中 |
| **Karing / Stash / Shadowrocket** （sing-box 内核） | `GET /%3Fip%3D1.2.3.4%3A443 HTTP/1.1` | ❌ `?` 被强制 URL-encode，Worker 找不到 ip → 回落默认 |

### ✅ 解决方案（三选一）

#### 方案 A：用 `/ip=` 替代 `/?ip=`（强烈推荐 ⭐）

Cloudflare-Vless Worker 的路径解析正则同时支持两种写法。
直接把 path 写成 `/ip=IP:PORT`（无 `?`），**V2RayN / Karing / Shadowrocket 全平台通吃**。

#### 方案 B：用 `index.html` 一键切换

打开 [`index.html`](./index.html) → 配置面板里勾选 **🍎 Karing / sing-box 兼容模式**，
所有节点链接和拼装器立即从 `/?ip=` 切到 `/ip=`，状态自动保存到 localStorage。

#### 方案 C：在 Karing 中关闭 0-RTT

Karing → 节点编辑 → 高级 → 找 **"Early Data" / "max-early-data" / "ws-opts.0-rtt"** → 关闭。

### 📲 客户端开关推荐

| 客户端 | 建议 |
|---|---|
| 📱 V2RayN / V2RayNG (Android) | **关闭** 兼容模式（默认即可） |
| 🍎 Karing / Stash / Surge (iOS/macOS) | **开启** 兼容模式 |
| 🐱 Clash Meta / Mihomo | 两者皆可，**建议开启** |
| 🍞 Shadowrocket | **开启**（sing-box 系生态） |
| ⚙️ V2Box / FoxRay | **开启** |

---

## 🖥️ 在线网页工具 `index.html`

### ✨ 功能特性

- 🎨 **Tailwind v4 + 玻璃拟态 UI** + 深色科技配色 + 渐变字体
- ⚙️ **零预填**：UUID / CF 域名 / 落地节点完全由用户自备
- ☑️ **多选订阅**：每张节点卡有复选框，仅勾选的节点会出现在批量订阅区
- 🍎 **Karing 兼容开关**：一键切换 `/?ip=` ↔ `/ip=`，自动持久化
- 📱 **二维码导出**：每张卡片配二维码按钮，扫码导入手机客户端
- 📦 **批量订阅**：复制全部 / 复制 Base64 / 下载 `sub.txt`
- 📖 **完整教学**：项目介绍、3 步部署、path 详解、链接解剖图、5 步拼装手册、FAQ
- 🛠️ **交互式拼装器**：教学章节末尾，单独填一组 UUID + IP + 域名 + 备注，实时生成

### 🔧 技术栈

| 技术 | 用途 |
|---|---|
| **Tailwind CSS v4**（browser runtime） | UI 工具类系统、`@theme` 设计令牌 |
| 纯原生 JS | 无任何 npm 依赖，单文件部署 |
| `localStorage` | UUID / 域名 / 节点文本 / 已选 / Karing 开关持久化 |

### 本地预览

```bash
# 方式 1：直接双击 index.html 用浏览器打开（已做剪贴板兼容回退）
# 方式 2：用 Python 起一个本地服务器
python -m http.server 8080
# 浏览器打开 http://localhost:8080
# 方式 3：VS Code 装 Live Server 插件 → 右键 index.html → Open with Live Server
```

### 部署到 Cloudflare Pages（推荐）

```bash
# 把本仓库推到你自己的 GitHub
# CF Dashboard → Pages → 连接 GitHub 仓库 → 部署
# Build settings 全部留空，Output dir 也留空，部署完成即可访问
```

---

## 🔒 隐私与本地存储

- 本工具 **100% 在浏览器本地运行**，所有数据（UUID、域名、节点列表、勾选、Karing 开关）只存在你的 `localStorage` 里
- **不会**向任何服务器上传你的配置（除了二维码用的 `api.qrserver.com` 外链生成图片）
- 想清除所有本地数据：浏览器开发者工具 → Application / 存储 → Local Storage → 删除 `https://<你的域名>` 下的所有键

| localStorage Key | 内容 |
|---|---|
| `nodesInputText` | 你录入的节点文本框原文 |
| `selectedNodeKeys` | 已勾选的节点（`ip:port` 数组） |
| `karingMode` | Karing 兼容模式开关 |

---

## ❓ 常见问题 FAQ

<details>
<summary><b>顶部 UUID / 域名 / 节点为什么都是空的？</b></summary>

这是有意为之 —— 本工具**完全不预填任何示例数据**，所有信息都需要你自备。
请按 [使用指南](#-使用指南核心流程) 填入你自己的 UUID、CF 域名、落地 IP。
</details>

<details>
<summary><b>path 改了之后，UUID 和域名也要改吗？</b></summary>

不用。UUID = 你的「身份证」、域名 = 「分拣中心地址」，两者只跟 Worker 本身有关。path 只决定流量送往哪个落地，可以随便改。
</details>

<details>
<summary><b>不写 path 会怎样？</b></summary>

会走 Worker 脚本里写死的 `默认备用小可爱地址` 那个兜底落地。也能用，但所有节点都跑同一个落地，没法切区。
</details>

<details>
<summary><b>为什么 Karing 怎么切落地都是默认 IP，V2RayN 就正常？</b></summary>

Karing 用 sing-box 内核，会把 path 里的 `?` 强制 URL-encode，Worker 解析不到 `ip` 参数 → 回落到默认落地。
**解决办法**：把 path 从 `/?ip=...` 改成 `/ip=...`（无 `?`），或在 `index.html` 打开「🍎 Karing 兼容模式」一键切换。详见 [客户端兼容性章节](#-客户端兼容性karing--v2rayn--sing-box)。
</details>

<details>
<summary><b>Snippets 部署一连就断怎么办？</b></summary>

Worker 脚本顶部 `CFG.concur` 默认是 `4`，**Snippets 不支持高并发连接**，必须改成 `1`：
```js
const CFG = { id: '...', concur: 1, /* 其余不动 */ };
```
Worker 部署则保持 `4` 不变（性能更好）。
</details>

<details>
<summary><b>端口 3306 / 2087 不是数据库 / 控制面板端口吗，安全吗？</b></summary>

这里的端口是**落地服务器对外开放的反代入口**，不是数据库。Cloudflare 出站允许这些端口，所以才能用。客户端始终只连 443，整个链路 TLS 加密。
</details>

<details>
<summary><b>落地 IP 失效了怎么办？</b></summary>

① 在文本框里把那一行删掉，换一个你自备的落地；② 改用远程 TXT 订阅写法 `/?ip=https://.../xxx.txt`，自动随机挑一个可用 IP（详见 path 写法 ③）。
</details>

<details>
<summary><b>Cloudflare 优选 IP 跟 path 有冲突吗？</b></summary>

没有。优选 IP 是替换链接里 `@` 后面的 address，让客户端连更快的 CF 入口；path 决定落地。两者各管一段，互不影响。
</details>

<details>
<summary><b>支持 SOCKS5 落地吗？</b></summary>

**Worker 脚本本身不支持。** 只支持「IP / 域名 / TXT 订阅」三种 path 写法，不支持 SOCKS5。
如需 SOCKS5，请改用其他 fork（如 BPB-Worker-Panel）。
</details>

<details>
<summary><b>V2RayNG 测试连通正常，浏览网页却提示「DNS 找不到」？</b></summary>

「测试连通」只验证 TCP 能到落地，**不解析域名**。修复步骤：
1. **远程 DNS 改成** `https://dns.google/dns-query`（**别用 `https://1.1.1.1/dns-query`**，1.1.1.1 在国内常被封）
2. 开启「**域名嗅探 Sniffing**」
3. 路由模式选 **「全局」** 先测一下
4. 域名策略选 `IPIfNonMatch`
</details>

<details>
<summary><b>index.html 里的复制按钮在本地 file:// 下点了没反应？</b></summary>

已做兼容。代码做了三级回退：Clipboard API → execCommand → prompt 手动复制。如果还不行，请检查是否被浏览器扩展（如某些隐私插件）拦截。
</details>

<details>
<summary><b>我清空了文本框，下次打开又冒出来了？</b></summary>

是 `localStorage` 缓存的。点工具栏「**清空全部**」（带确认弹窗）会同步清除 localStorage 里的节点文本与已选状态。
</details>

---

## 🙏 致谢 & 相关项目

| 项目 | 用途 |
| --- | --- |
| 🔧 [hofccyf/Cloudflare-Vless](https://github.com/hofccyf/Cloudflare-Vless) | 核心 Worker 脚本（VLESS over WS + TLS） |
| 🌐 [ethgan/Cloudflare_TextSpace_Worker](https://github.com/ethgan/Cloudflare_TextSpace_Worker) | TextSpace Worker 上游 |
| 🚀 [eooce/Cloudflare-proxy](https://github.com/eooce/Cloudflare-proxy) | Shadowsocks 协议参考（老王大佬） |
| 🔁 [ToiCF/CF-Workers-TURN](https://github.com/ToiCF/CF-Workers-TURN) | TURN 协议参考（Ak 大佬） |
| 🎨 [Tailwind CSS v4](https://tailwindcss.com/) | `index.html` UI 工具类系统 |
| 📷 [api.qrserver.com](https://goqr.me/api/) | 节点二维码生成（仅在点击「二维码」时使用） |

---

## ⚠️ 免责声明

- 本项目仅作**网络技术学习与研究**用途，**不存储、不分发任何代理服务，也不提供任何落地 IP**
- 所有 UUID、域名、落地 IP 均由使用者**自行准备并自行承担**其合法性与可用性
- 使用者**必须遵守当地法律法规**，由此产生的任何后果由使用者自行承担
- 本工具是一个**纯前端链接拼装计算器**，不参与任何代理流量

---

<div align="center">

**自备配置 · 本地生成 · 零依赖 · 零跟踪 ❤️**

⭐ 如果对你有帮助，欢迎 Star

</div>
