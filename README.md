# SSH Key 登录 DMIT VPS 完整教程：从零开始到安全上手

刚买了 DMIT 的 VPS，结果登录的时候傻眼了——不是普通的账号密码，而是一个 `.pem` 文件？

这其实是 DMIT 的标准操作：默认使用 SSH Key（密钥对）认证登录，比密码安全得多，不容易被暴力破解。但对于第一次接触的朋友来说，确实有点懵。

别担心，这篇文章把 SSH Key 登录 DMIT VPS 的整个流程捋清楚，Windows、macOS、Linux 全覆盖，顺带聊聊 DMIT 现在有哪些套餐值得入手。

---

## 什么是 SSH Key 登录？为什么 DMIT 要用它

简单说，SSH Key 是一对密钥——**公钥放服务器上，私钥自己保管**。登录时服务器用公钥验证你的私钥，匹配就放行。

和密码登录比：

- 密码可以被猜、被爆破、被钓鱼
- SSH Key 要破解，理论上需要数年算力，基本等于不可能

DMIT 在你创建 VPS 的时候自动生成一对密钥，私钥只能下载一次，所以**下载后一定要妥善保存**。如果丢了，需要在控制面板重新生成，VPS 也要重启。

---

## 第一步：从 DMIT 控制面板获取 SSH Key

登录 DMIT 控制面板（用你注册时的账号密码）：

1. 进入「Services」→ 找到你的 VPS → 点击管理
2. 找到「SSH Key Management」或「管理 SSH 密钥」
3. 如果还没生成密钥，点击生成
4. 下载私钥文件：
   - `.pem` 格式 → macOS / Linux / Windows OpenSSH 用
   - `.ppk` 格式 → Windows PuTTY 专用

> **重要**：私钥文件只能下载一次！下载后立刻备份到安全位置（比如加密的 U 盘或密码管理器附件）。

---

## 第二步：找到你的 VPS 连接信息

在控制面板里记下：
- **IP 地址**：你的 VPS 公网 IP
- **SSH 端口**：DMIT 默认通常是 `22`，部分套餐可能不同
- **用户名**：一般是 `root`

---

## macOS / Linux 用户：Terminal 连接

macOS 和 Linux 系统内置了 SSH 客户端，不需要安装任何软件。

### 操作步骤

**1. 打开 Terminal（终端）**

macOS：`Command + Space` 搜索「Terminal」打开

**2. 设置私钥文件权限**

SSH 对私钥文件的权限有严格要求，权限太宽松直接拒绝连接：

bash
chmod 600 /path/to/your-key.pem


把 `/path/to/your-key.pem` 替换成你实际的文件路径，比如：

bash
chmod 600 ~/Downloads/dmit-key.pem


**3. SSH 连接命令**

bash
ssh -i /path/to/your-key.pem root@你的VPS_IP -p 端口号


实际例子：

bash
ssh -i ~/Downloads/dmit-key.pem root@123.456.789.0 -p 22


**4. 首次连接确认**

第一次连接会提示：


Are you sure you want to continue connecting (yes/no)?


输入 `yes` 回车，服务器指纹会被记录，下次就不会再问了。

### 常见问题

**报错 `WARNING: UNPROTECTED PRIVATE KEY FILE!`**
→ 私钥权限不对，重新执行 `chmod 600` 命令

**报错 `Permission denied (publickey)`**
→ 检查 IP、端口、用户名是否正确；确认使用的是正确的 `.pem` 文件

**连接超时**
→ 检查 IP 地址是否正确；检查 VPS 是否正常运行

---

## Windows 用户：三种连接方式

Windows 用户有多种选择，按照习惯来就好。

### 方式一：Windows 内置 OpenSSH（推荐）

Windows 10 和 Windows 11 已经内置了 OpenSSH 客户端，不需要额外安装。

**打开 PowerShell 或命令提示符（CMD）**，执行：

bash
ssh -i C:\path\to\your-key.pem root@你的VPS_IP -p 端口号


注意 Windows 路径格式，例如：

bash
ssh -i C:\Users\YourName\Downloads\dmit-key.pem root@123.456.789.0 -p 22


Windows 的 OpenSSH 也需要私钥文件权限设置正确。如果报权限错误，右键 `.pem` 文件 → 属性 → 安全 → 只保留当前用户的读取权限，删除其他用户权限。

### 方式二：PuTTY（老牌工具）

如果你习惯图形界面，PuTTY 是经典选择。DMIT 控制面板提供 `.ppk` 格式就是专门给 PuTTY 用的。

**操作步骤：**

1. 打开 PuTTY
2. 在「Host Name」填入 VPS IP 地址
3. 「Port」填入端口号
4. 左侧菜单：Connection → SSH → Auth → Credentials
5. 在「Private key file for authentication」点击 Browse，选择 `.ppk` 文件
6. 回到 Session，可以把配置保存（Save）方便下次用
7. 点击「Open」连接

首次连接会弹出安全提示，点击「Accept」即可。

### 方式三：XShell（功能更强的终端）

XShell 是国内用户常用的 SSH 客户端，界面友好，支持多标签管理多台服务器。

**操作步骤：**

1. 新建会话，填入 IP 和端口
2. 认证方式选择「Public Key（公钥）」
3. 点击「用户密钥」→「浏览」→ 导入 `.pem` 文件
4. 保存并连接

---

## 手机也能连？移动端 SSH 工具

偶尔需要在手机上管理服务器，可以用这两款：

**iOS / Android：Termius**
- 打开 Termius → 点击「Keychain」→「ADD KEY」
- 选择「Import from File」，导入 `.pem` 文件
- 新建主机，认证方式选 Key，选中刚导入的密钥

**Android：JuiceSSH**
- Connections → 新增连接
- Authentication → 新增 Identity
- 上传 `.pem` 私钥文件

---

## SSH Key 上传：用自己的密钥对

如果你有自己生成的 SSH Key 对（比如用 `ssh-keygen` 命令生成的），也可以上传公钥到 DMIT 使用。

**生成密钥对（如果还没有）：**

bash
ssh-keygen -t ed25519 -C "your@email.com"


一路回车，会在 `~/.ssh/` 目录生成 `id_ed25519`（私钥）和 `id_ed25519.pub`（公钥）两个文件。

**上传公钥到 DMIT：**

在 DMIT 控制面板 → SSH Key Management → 添加/上传 SSH Key，把 `id_ed25519.pub` 的内容粘贴进去。

DMIT 支持的密钥类型：RSA、Ed25519（EdDSA）、FIDO U2F 安全密钥等。

---

## DMIT VPS 套餐对比

说到这里顺便聊聊 DMIT 的套餐，毕竟选对了才能用得舒心。

DMIT 主打面向中国大陆优化的 VPS，走 CN2 GIA、CMIN2 等优质线路，延迟低、速度稳，这是它的核心竞争力。

### 洛杉矶（LAX）系列

| 套餐 | CPU | 内存 | 硬盘 | 流量/带宽 | 价格 | 购买 |
|------|-----|------|------|-----------|------|------|
| LAX PRO WEE | 1核 | 1GB | 10GB SSD | 450GB @500Mbps | $36.90/年 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| LAX EB WEE | 1核 | 1GB | — | 800GB @1Gbps | $39.90/年 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| LAX TINY | 1核 | 1GB | 20GB SSD | — | $6.90/月 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| LAX STARTER | 2核 | 2GB | 40GB SSD | — | $12.90/月 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| LAX MINI | 2核 | 4GB | 80GB SSD | — | $21.90/月 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| LAX MICRO | 4核 | 4GB | 120GB SSD | — | $32.90/月 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |

### 香港（HKG）系列

| 套餐 | CPU | 内存 | 硬盘 | 流量 | 价格 | 购买 |
|------|-----|------|------|------|------|------|
| HKG T1 WEE | 1核 | 1GB | 20GB SSD | — | $36.90/年 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| HKG T1 TINY | 1核 | 1GB | — | — | $6.90/月 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| HKG AS3 Pro STARTER | 1核 | 2GB | 40GB SSD | 1000GB双向 | $79.90/月 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |
| HKG AS3 Pro MINI | 2核 | 2GB | 60GB SSD | 1500GB双向 | $119.90/月 | 👉 [立即购买](https://www.dmit.io/aff.php?aff=18446) |

> 💡 香港 AS3 Pro 系列走优质直连线路，对访问延迟要求高的用户（比如做实时业务）很合适，但价格也贵一些。

---

## 当前优惠码汇总

DMIT 目前有几个比较实惠的长期折扣码，**非月付**才能用：

| 优惠码 | 适用套餐 | 折扣 | 备注 |
|--------|----------|------|------|
| `LAX-EB-LAUNCH-NON-MONTHLY-RECURRING-20OFF` | LAX Eyeball 系列 | 永久 8 折 | 季付或年付 |
| `HKG-T1-ANNUALLY-45OFF-RECUR` | HKG T1 年付 | 永久 55 折 + 配置升级 | 额外赠送 CPU/内存/硬盘 |
| `2025-TYO-T1-HI-GSL-NON-MONTHLY-30OFF` | 日本 VPS | 永久 7 折 | 季付或年付 |

使用方式：结账时在优惠码框填入，点击应用即可。

👉 [前往 DMIT 查看最新套餐与折扣](https://www.dmit.io/aff.php?aff=18446)

---

## SSH Key 安全最佳实践

用了 DMIT 之后，建议养成这几个习惯：

**1. 私钥绝对不要上传到 GitHub 或任何公开平台**
每隔一段时间 GitHub 都有人手滑把私钥传上去，然后服务器就被黑了。

**2. 私钥文件设置严格权限**
bash
chmod 600 ~/.ssh/id_rsa.pem

Windows 用户确保只有自己的账号能读取。

**3. 考虑给私钥设置 passphrase（密码短语）**
`ssh-keygen` 生成时可以设置，即使私钥文件泄露，没有密码短语也连不上。代价是每次连接要输入这个密码（可以用 ssh-agent 缓解）。

**4. 定期轮换密钥**
如果长期用同一台服务器，建议每半年换一次密钥。DMIT 控制面板支持重新生成。

**5. 禁用密码登录**
有了 SSH Key，可以在服务器上禁用密码认证，进一步降低被爆破的风险：

bash
# 编辑 SSH 配置文件
nano /etc/ssh/sshd_config

# 找到并修改
PasswordAuthentication no

# 重启 SSH 服务
systemctl restart sshd


注意：**在禁用密码登录之前，确保 SSH Key 能正常连接**，否则可能把自己锁在外面。

---

## 私钥丢了怎么办？

这是很多人的噩梦，但 DMIT 有应对方案：

1. 登录 DMIT 控制面板
2. 找到对应 VPS → SSH Key Management
3. 点击「Regenerate」重新生成密钥对
4. 下载新的私钥文件
5. VPS 会重启一次（服务会短暂中断）

新密钥生成后，旧的就失效了。重启完成后用新私钥重新连接即可。

---

## 小结

SSH Key 登录 DMIT VPS 其实就这几步：

1. **控制面板下载私钥**（.pem 或 .ppk）
2. **设置文件权限**（chmod 600，仅限 macOS/Linux）
3. **用对工具连接**（Terminal、PuTTY、XShell 任选）
4. **妥善保管私钥**，不丢、不外传

第一次操作可能有点生疏，跟着步骤来一遍基本就顺了。DMIT 的控制面板做得比较直观，生成密钥、查看 IP 这些操作都在同一个地方，不用到处找。

如果你还没有 DMIT 的 VPS，现在入手年付套餐配合折扣码性价比还不错——👉 [点这里查看最新套餐](https://www.dmit.io/aff.php?aff=18446)
