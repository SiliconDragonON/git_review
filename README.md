# Git 详细介绍

## 一、什么是 Git

**Git** 是一个**分布式版本控制系统**（Distributed Version Control System, DVCS），用于跟踪文件的变化，协调多人协作开发。它由 Linus Torvalds 于 2005 年创建，是目前世界上最流行的版本控制系统。

---

## 二、在文件夹中创建代码仓库

### 使用的命令

```bash
git init
```

### 执行流程

假设你有一个文件夹 `my_project`：

```bash
# 进入你的项目文件夹
cd /f/git_review

# 初始化 Git 仓库
git init
```

### 执行后会发生什么

执行 `git init` 后，Git 会在该文件夹的**根目录下创建一个名为 `.git` 的隐藏目录**。这个目录包含了 Git 仓库的全部核心数据结构和配置文件。

```
my_project/
├── .git/                    # ← 新创建的隐藏目录，Git 的"大脑"
│   ├── HEAD                 # 指向当前检出的分支的引用
│   ├── config               # 仓库级别的配置文件
│   ├── description          # 仓库的描述（供 GitWeb 等使用）
│   ├── hooks/               # 客户端钩子脚本目录
│   │   ├── pre-commit.sample
│   │   ├── post-commit.sample
│   │   └── ...
│   ├── info/
│   │   └── exclude          # 本地排除文件（类似 .gitignore）
│   ├── objects/             # Git 对象数据库（存储所有数据）
│   │   ├── info/
│   │   └── pack/
│   └── refs/                # 分支和标签的引用
│       ├── heads/           # 本地分支引用
│       └── tags/            # 标签引用
└── (你的项目文件...)
```

### `.git` 目录中各部分详解

| 文件/目录 | 作用 |
|-----------|------|
| **objects/** | **Git 对象数据库**，存储所有 commit、tree、blob、tag 对象。Git 将数据以内容寻址（SHA-1 哈希）的方式存储，每个对象以哈希值的前两位作为子目录名，剩余部分作为文件名 |
| **refs/heads/** | 存放每个**分支**的引用文件，文件内容是该分支指向的最新 commit 的 SHA-1 值 |
| **refs/tags/** | 存放**标签**的引用 |
| **HEAD** | 一个符号引用，指向当前你在哪个分支上。内容通常是 `ref: refs/heads/main` |
| **config** | 仓库级别的配置，例如远程仓库地址、分支的追踪关系等 |
| **hooks/** | 存放**钩子脚本**，可以在特定 Git 事件（commit、push 等）时自动触发 |
| **info/exclude** | 类似于 `.gitignore`，但仅作用于本机，不会被提交到仓库中 |

### 执行之后的状态

执行 `git init` 后：
- 项目中的**文件并不会被自动追踪**，它们处于 "Untracked"（未追踪）状态
- 当前处于默认分支（通常是 `main` 或 `master`）
- 需要手动执行 `git add` 和 `git commit` 来进行版本控制

---

## 三、Git 的核心设计思想

### 1. 分布式架构
与 SVN 等集中式版本控制系统不同，Git 每个开发者的机器上都拥有**完整的仓库副本和历史记录**。这意味着即使没有网络，你也可以进行提交、查看历史、创建分支等所有操作。

### 2. 内容寻址存储
Git 不以文件名或差异来存储数据，而是对所有内容计算 SHA-1 哈希值，以**快照**的方式存储。如果文件没有变化，Git 不会重复存储，而是保留一个指向之前版本的链接。

### 3. 三种状态和三个区域

```
工作目录                暂存区                  本地仓库
(Working Directory)    (Staging Area)          (Local Repository)
       │                    │                       │
       │     git add        │      git commit       │
       │ ────────────────→  │ ────────────────────→ │
       │                    │                       │
       │          git checkout / git restore        │
       │ ←───────────────────────────────────────── │
```

| 区域 | 说明 |
|------|------|
| **工作目录** | 你实际编辑文件的地方，从 `.git/objects` 中提取特定版本的文件 |
| **暂存区** | 存储在 `.git/index`，记录了你准备提交到下一次 commit 的文件快照 |
| **本地仓库** | `.git` 目录本身，存储了所有提交历史和元数据 |

---

## 四、Git 的诞生历史

### 背景：BitKeeper 的退出

2002 年之前，Linux 内核社区一直使用**补丁和 tarball** 的方式管理代码，效率极低。2002 年，Linux 社区开始使用一个名为 **BitKeeper** 的分布式版本控制系统，它是商业软件，但对开源社区提供了免费许可。

2005 年，一位 Linux 内核开发者（Andrew Tridgell）尝试对 BitKeeper 进行逆向工程。BitKeeper 的开发商 **BitMover** 对此不满，决定**撤回对 Linux 社区的免费许可**。

### Linus 的决定

Linus Torvalds 面临的问题：
- BitKeeper 不能用了
- 现有的开源版本控制系统（如 SVN、CVS）性能太差，不能满足 Linux 内核这样超大规模项目的需求
- 他需要一个**高性能、分布式、安全可靠**的系统

Linus 在 BitKeeper 撤出后，闭关了大约一周时间，于 **2005 年 4 月 7 日** 发布了 Git 的第一个版本。

### Git 名字的由来

Linus 曾开玩笑说：*"I'm an egotistical bastard, and I name all my projects after myself. First 'Linux', now 'git'."*

- **"git"** 在英式俚语中是"蠢人、饭桶"的意思
- 也可以解读为 **"Global Information Tracker"**（全局信息追踪器）的缩写

### 关键时间线

| 时间 | 事件 |
|------|------|
| **2002** | Linux 社区开始使用 BitKeeper |
| **2005.04** | BitMover 撤回免费许可 |
| **2005.04.03** | Linus 开始编写 Git |
| **2005.04.07** | Git 发布初始版本，能够自举（用 Git 管理 Git 的源码） |
| **2005.04.18** | Git 实现了首次合并（merge）操作 |
| **2005.06** | Git 已经可以管理 Linux 内核 2.6.12 的发布 |
| **2005.07** | Linus 将维护权交给 **Junio Hamano**（滨野纯），至今他仍是 Git 的主要维护者 |
| **2008** | **GitHub** 上线，极大地推动了 Git 的普及 |
| **2010+** | Git 成为最流行的版本控制系统，至今仍占主导地位 |

### Git 设计目标的体现

Linus 在创建设计时设定了几个核心目标：

1. **速度** —— 打补丁和比较差异必须在秒级完成
2. **简洁的设计** —— 避免不必要的复杂性
3. **对非线性开发的强大支持** —— 成千上万个并行分支
4. **完全分布式** —— 每个节点都是完整的仓库
5. **能够高效处理像 Linux 内核这样的大型项目** —— 速度和数据大小都必须优秀

这些目标至今仍然是 Git 相对于其他版本控制系统的核心优势。

---

## 五、Git 仓库大小变化机制

### 核心问题：文件变小了，仓库会变小吗？

**不会。** 这是理解 Git 存储模型最关键的一点。

### 1. Git 以快照存储，而非差异

每次 `git add` + `git commit`，Git 会对**整个文件**计算 SHA-1 哈希，生成一个 **blob 对象**存入 `.git/objects/`。这是完整副本，不是差异。

```
原始文件 (10MB) → blob A (10MB, SHA: aaa...)
修改后 (1KB)   → blob B (1KB,  SHA: bbb...)
```

两个 blob 都会存在于 `.git/objects/` 中。

### 2. 两个存储阶段

#### 阶段 1：松散对象（Loose Objects）

刚 commit 完，Git 将每个 blob 独立存储为 `.git/objects/` 下的一个文件（以 zlib 压缩）：

```
.git/objects/
├── aa/
│   └── aaaaa...    # 10MB 的 blob（zlib 压缩后可能 ~5-7MB）
├── bb/
│   └── bbbbb...    # 1KB 的 blob
```

此时仓库大小 ≈ 10MB + 1KB（均被 zlib 压缩）。

#### 阶段 2：打包文件（Packfiles）

当你执行 `git gc`（或 Git 自动触发垃圾回收），Git 会将松散对象打包成 **packfile**。在 packfile 中，Git 会进行 **delta 压缩**（差异压缩）：

```
packfile 内部结构：
┌────────────────────────────────────┐
│ blob A (10MB)  — 作为基准          │  ← 完整存储，必须保留
│ blob B (1KB)   — 对 A 的 delta    │  ← 只存差异
└────────────────────────────────────┘
```

**但 delta 压缩只节省 blob B 的空间**——blob A（10MB）仍然完整保留在 packfile 中，因为它是基准。

### 3. 为什么旧文件不能删除？

因为 Git 是**不可变历史**模型。旧 commit 指向旧 blob，如果删除那个 blob，commit 链就断了：

```
commit C1 ──→ tree ──→ blob A (10MB)   ← 历史需要它，必须存在
commit C2 ──→ tree ──→ blob B (1KB)    ← 当前版本
```

只要 C1 还存在，blob A 就**必须存在**。因此：

```
┌───────────────────────────────────────────────────────────┐
│                                                           │
│   仓库总大小 = 当前快照 + 整个历史链上的所有旧快照           │
│                                                           │
│   文件变小了，仓库不会变小 —— 旧的 10MB 永存于历史中！       │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

### 4. 不同场景下的仓库大小

| 场景 | 仓库实际大小 |
|------|-------------|
| commit 了大文件后立即修改为小文件 | **大文件 + 小文件都在仓库里** |
| `git gc` 之后 | 两个都压缩了，但**大文件仍然存在** |
| clone 这个仓库的人 | 仍然需要下载那 10MB 的历史 |

### 5. 如何真正从仓库中清除大文件

如果那 10MB 是你不小心提交的、不需要保留在历史中，需要用**重写历史**的工具：

```bash
# 方法 1：使用 git filter-repo（推荐，速度最快）
git filter-repo --path 大文件路径 --invert-paths

# 方法 2：使用 BFG Repo-Cleaner（简单易用）
bfg --delete-files 大文件路径

# 方法 3：使用 git filter-branch（传统方式，较慢）
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch 大文件路径" \
  --prune-empty -- --all
```

这实际上是**生成新的 commit 对象**，旧的 blob A 不再被任何引用指向，最终被 `git gc --aggressive` 回收。

### 6. 动手实验

```bash
# 1. 生成一个 10MB 文件并提交
dd if=/dev/urandom of=bigfile.bin bs=1M count=10
git add bigfile.bin
git commit -m "add 10MB file"

# 2. 查看仓库大小
du -sh .git

# 3. 把文件变小并提交
echo "tiny" > bigfile.bin
git add bigfile.bin
git commit -m "shrink to tiny"

# 4. 再看仓库大小——基本不变！
du -sh .git

# 5. 运行垃圾回收后，仍然包含 10MB 的历史
git gc
du -sh .git
```

### 7. 小结

| 问题 | 答案 |
|------|------|
| Git 存的是快照还是差异？ | **快照（完整文件副本）**，但在 packfile 中会做 delta 压缩 |
| 文件变小了仓库会变小吗？ | **不会**——旧版本永存于历史中 |
| delta 压缩省的是什么？ | 省的是**新版本**的空间，但**旧版本仍然是基准，必须完整保留** |
| 如何真正删除？ | 用 `git filter-repo` 等工具**重写历史**，丢弃引用后再 `git gc` |

---

## 六、GitHub 与 Git 的关系及历史发展

### 1. Git 和 GitHub 的关系

很多人把 Git 和 GitHub 混为一谈，但它们是完全不同的东西：

| 对比维度 | **Git** | **GitHub** |
|----------|--------|--------|
| **本质** | 分布式版本控制**工具** | 基于 Git 的代码托管**平台**（Web 服务） |
| **创建者** | Linus Torvalds | Tom Preston-Werner、Chris Wanstrath、PJ Hyett |
| **创建时间** | 2005 年 | 2008 年 |
| **运行方式** | 本地命令行工具，不需要网络 | 云端 SaaS 服务，需要网络 |
| **功能** | 版本控制、分支管理、合并等 | 代码托管、Pull Request、Issue 追踪、CI/CD、Wiki 等 |
| **同类对比** | SVN、Mercurial | GitLab、Bitbucket、Gitee |

**类比**：Git 就像拍照的**相机**，GitHub 就像分享照片的**相册网站**。你可以用相机拍照而不上传，但没有相机就无法拍照。同理，你可以只用 Git 而不使用 GitHub，但没有 Git 就无法使用 GitHub。

### 2. GitHub 的历史发展

| 时间 | 事件 |
|------|------|
| **2007.10** | Tom Preston-Werner 和 Chris Wanstrath 开始开发 GitHub |
| **2008.04** | GitHub 正式上线公测 |
| **2009** | GitHub 获得 10 万用户 |
| **2011** | GitHub 超越 SourceForge 和 Google Code，成为最受欢迎的代码托管平台 |
| **2012** | GitHub 获得 1 亿美元融资（Andreessen Horowitz 领投） |
| **2015** | GitHub 在中国因 DDoS 攻击被短暂封锁 |
| **2018.06** | **微软以 75 亿美元收购 GitHub**，Nat Friedman 出任 CEO |
| **2019** | GitHub 发布 Actions（CI/CD）、Packages、Sponsors 等新功能 |
| **2020** | GitHub 发布 Codespaces（云端开发环境），并宣布所有核心功能免费 |
| **2024+** | GitHub Copilot（AI 编程助手）持续发展，拥有超过 1 亿开发者 |

### 3. 将本地仓库推送到 GitHub 的完整流程

#### 前提条件

```bash
# 1. 安装 Git
# 2. 注册 GitHub 账号：https://github.com
# 3. 配置 SSH 密钥（推荐）或使用 HTTPS + Personal Access Token
```

#### 步骤一：配置 SSH 密钥（如已配置可跳过）

```bash
# 生成 SSH 密钥对
ssh-keygen -t ed25519 -C "your_email@example.com"

# 查看公钥内容，复制到 GitHub → Settings → SSH and GPG keys → New SSH key
cat ~/.ssh/id_ed25519.pub

# 测试连接
ssh -T git@github.com
# 成功显示：Hi <username>! You've authenticated...
```

#### 步骤二：在 GitHub 上创建远程仓库

```
方法 A：在 GitHub 网页上操作
  1. 点击右上角 "+" → "New repository"
  2. 填写仓库名称
  3. 选择 Public 或 Private
  4. 不要勾选 "Add a README file"（本地已有仓库的话）
  5. 点击 "Create repository"

方法 B：使用 GitHub CLI
  gh repo create my-project --public --source=. --remote=origin
```

#### 步骤三：连接本地仓库并推送

```bash
# 场景 A：本地已有仓库（执行了 git init 和 commit）

# 1. 添加远程仓库地址
git remote add origin git@github.com:你的用户名/你的仓库名.git
# 如果是 HTTPS 方式：
git remote add origin https://github.com/你的用户名/你的仓库名.git

# 2. 设置主分支名称（如果还不是 main）
git branch -M main

# 3. 推送本地代码到 GitHub
git push -u origin main
# -u 参数：将本地 main 分支与远程 origin/main 建立追踪关系，之后只需 git push 即可

# 场景 B：本地还没有仓库，从头开始

mkdir my_project && cd my_project
git init
echo "# My Project" > README.md
git add .
git commit -m "first commit"
git remote add origin git@github.com:你的用户名/你的仓库名.git
git branch -M main
git push -u origin main
```

#### 推送后发生了什么

```
本地仓库                                        GitHub 远程仓库
┌─────────────────────┐                    ┌──────────────────────┐
│ commit C1           │                    │ commit C1            │
│ commit C2           │  ── git push ──→   │ commit C2            │
│ commit C3           │                    │ commit C3            │
│ refs/heads/main →C3 │                    │ refs/heads/main →C3  │
└─────────────────────┘                    └──────────────────────┘

推送之后，GitHub 上就有了你本地的全部 commit 历史、分支和标签。
```

### 4. 本地仓库的配置与设置

#### Git 配置的三个层级

| 层级 | 配置文件位置 | 作用范围 |
|------|-------------|---------|
| **System（系统级）** | `/etc/gitconfig` | 该机器上所有用户和所有仓库 |
| **Global（全局级）** | `~/.gitconfig` | 当前用户的所有仓库 |
| **Local（仓库级）** | `.git/config` | 仅当前仓库 |

**优先级：Local > Global > System**

#### 基础身份配置

```bash
# 全局配置（影响所有仓库）
git config --global user.name "你的名字"
git config --global user.email "your_email@example.com"

# 对单个仓库设置不同的身份（在仓库目录中执行）
git config user.name "公司用名"
git config user.email "work@company.com"

# 查看所有配置
git config --list
git config --global --list
git config --local --list
```

#### 常用核心配置

```bash
# 设置默认分支名（Git 2.28+）
git config --global init.defaultBranch main

# 设置默认编辑器
git config --global core.editor "code --wait"    # VS Code
git config --global core.editor "vim"

# 启用颜色输出
git config --global color.ui auto

# 设置换行符处理（Windows 开发，仓库使用 LF）
git config --global core.autocrlf true     # Windows 上检出 CRLF，提交转 LF
git config --global core.autocrlf input    # Mac/Linux 上提交转 LF

# 设置别名（简化常用命令）
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.lg "log --oneline --graph --all --decorate"
# 之后 git co = git checkout, git st = git status, git lg = 漂亮的提交图
```

#### 远程仓库配置

```bash
# 添加远程仓库
git remote add origin git@github.com:用户名/仓库名.git

# 查看远程仓库信息
git remote -v
# origin  git@github.com:用户名/仓库名.git (fetch)
# origin  git@github.com:用户名/仓库名.git (push)

# 添加多个远程仓库（例如同时推送到 GitHub 和 GitLab）
git remote add github git@github.com:用户名/仓库名.git
git remote add gitlab git@gitlab.com:用户名/仓库名.git

# 修改远程仓库地址
git remote set-url origin git@github.com:新用户名/新仓库.git

# 删除远程仓库
git remote remove gitlab

# 查看远程仓库详细信息
git remote show origin
```

#### 忽略文件配置（`.gitignore`）

```bash
# 在项目根目录创建 .gitignore 文件
cat > .gitignore << 'EOF'
# 操作系统
.DS_Store
Thumbs.db

# 编译产物
node_modules/
dist/
build/
*.exe
*.o

# 环境变量
.env
.env.local

# IDE 配置
.vscode/
.idea/

# 日志
*.log
EOF

# 添加到仓库
git add .gitignore
git commit -m "add .gitignore"
```

#### 完整的仓库配置文件示例（`.git/config`）

连接远程仓库后，本地的 `.git/config` 会变成类似这样：

```ini
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true

[remote "origin"]
    url = git@github.com:你的用户名/你的仓库名.git
    fetch = +refs/heads/*:refs/remotes/origin/*

[branch "main"]
    remote = origin
    merge = refs/heads/main

[user]
    name = 你的名字
    email = your_email@example.com
```

### 5. 日常推送与拉取的完整流程

```bash
# 1. 编辑代码后
git add .
git commit -m "描述你的改动"

# 2. 推送到 GitHub
git push

# 3. 如果别人也推送了代码，先拉取再推送
git pull                    # = git fetch + git merge
git push

# 4. 从 GitHub 克隆已有仓库
git clone git@github.com:用户名/仓库名.git

# 5. 克隆后，本地已经自动配置好 origin 远程仓库
git remote -v               # 可以看到 origin 已配置
```

### 6. SSH 与 HTTPS 两种方式的对比

| 对比维度 | **SSH** | **HTTPS** |
|----------|---------|-----------|
| **地址格式** | `git@github.com:user/repo.git` | `https://github.com/user/repo.git` |
| **认证方式** | SSH 密钥对（公钥上传到 GitHub） | Personal Access Token（代替密码） |
| **配置复杂度** | 稍高（需生成密钥） | 简单（输入 token 即可） |
| **安全性** | 高 | 高（使用 token） |
| **适用场景** | 个人开发、服务器部署 | 临时使用、共享环境 |
| **推荐** | ⭐ 推荐 | 备用方案 |

---

## 七、总结

| 要点 | 说明 |
|------|------|
| **初始化命令** | `git init` |
| **核心结果** | 创建 `.git` 隐藏目录，包含整个仓库的数据库和配置 |
| **创建者** | Linus Torvalds（同时也是 Linux 内核的创建者） |
| **创建时间** | 2005 年 4 月 |
| **创建原因** | BitKeeper 商业许可被撤回，需要一个新的分布式版本控制系统 |
| **当前维护者** | Junio Hamano（2005 年至今） |
| **仓库存储模型** | 以快照（完整副本）存储，packfile 中做 delta 压缩，但旧版本永远保留 |
| **Git vs GitHub** | Git 是版本控制工具（本地），GitHub 是代码托管平台（云端服务） |
| **本地推送到远程** | `git remote add origin <url>` → `git push -u origin main` |
| **配置层级** | System（系统级）> Global（全局级）> Local（仓库级） |

---

## 八、git config --list 输出详解

`git config --list` 会列出当前生效的**全部配置项**，按层级优先级合并显示。下面逐条说明常见的配置项及其含义。

### 1. 查看配置的分层命令

```bash
git config --list              # 列出所有生效的配置（三层合并）
git config --system --list     # 仅 System 级
git config --global --list     # 仅 Global 级
git config --local --list      # 仅 Local 级（当前仓库）
git config --list --show-origin  # 显示每条配置的来源文件路径
```

### 2. 典型输出及逐条解释

以下是一个 Windows 环境下的典型输出（已脱敏处理），按来源层级分析：

#### System（系统级）—— Git 安装时自动生成

| 配置项 | 典型值 | 含义 |
|--------|--------|------|
| `diff.astextplain.textconv` | `astextplain` | 将 `.doc`、`.pdf` 等二进制文件转为纯文本再做 diff |
| `filter.lfs.clean` | `git-lfs clean -- %f` | **Git LFS**：提交时将大文件替换为指针 |
| `filter.lfs.smudge` | `git-lfs smudge -- %f` | **Git LFS**：检出时将指针还原为大文件 |
| `filter.lfs.process` | `git-lfs filter-process` | **Git LFS**：长驻进程模式，比 clean/smudge 更快 |
| `filter.lfs.required` | `true` | 强制启用 LFS 过滤器 |
| `http.sslbackend` | `schannel` | 使用 **Windows 原生 Schannel** 做 SSL/TLS 加密（而非 OpenSSL） |
| `core.autocrlf` | `true` | ⭐ 提交时 `CRLF→LF`，检出时 `LF→CRLF`，解决 Windows 换行符问题 |
| `core.fscache` | `true` | 启用文件系统缓存，加速 Windows 上 `git status` 等操作 |
| `core.symlinks` | `false` | 禁用符号链接（Windows 默认不支持） |
| `pull.rebase` | `false` | `git pull` 时使用 merge 策略而非 rebase |
| `credential.helper` | `manager` | 使用 **Windows 凭据管理器** 存储远程仓库密码/Token |
| `credential.https://dev.azure.com.usehttppath` | `true` | Azure DevOps 凭据匹配使用完整路径 |
| `init.defaultbranch` | `master` 或 `main` | 新仓库的默认分支名称 |

#### Global（全局级）—— 用户级配置

| 配置项 | 典型值 | 含义 |
|--------|--------|------|
| `user.name` | `Your Name` | ⭐ commit 作者名（全局默认，所有仓库共用） |
| `user.email` | `your@email.com` | ⭐ commit 作者邮箱（关联 GitHub 账户） |
| `safe.directory` | `/path/to/repo` | 信任该目录，避免属主不匹配时触发安全警告 |

#### Local（仓库级）—— 当前仓库专属

| 配置项 | 典型值 | 含义 |
|--------|--------|------|
| `core.repositoryformatversion` | `0` | 仓库格式版本，`0` 为标准格式 |
| `core.filemode` | `false`（Windows）/ `true`（Unix） | 是否追踪文件的可执行权限位 |
| `core.bare` | `false` | 是否为裸仓库（bare repo），`false` 表示有工作目录 |
| `core.logallrefupdates` | `true` | 记录所有分支引用变更日志（`git reflog` 的数据来源） |
| `core.symlinks` | `false` | 是否支持符号链接 |
| `core.ignorecase` | `true`（Windows/macOS）/ `false`（Linux） | 文件名大小写是否敏感 |
| `user.name` | `repo-specific-name` | 可选：覆盖全局用户名，仅当前仓库生效 |
| `user.email` | `repo-specific@email.com` | 可选：覆盖全局邮箱，仅当前仓库生效 |
| `remote.origin.url` | `git@github.com:user/repo.git` | 远程仓库地址 |
| `branch.main.remote` | `origin` | 本地 `main` 分支对应的远程仓库名 |
| `branch.main.merge` | `refs/heads/main` | 本地 `main` 分支追踪的远程分支 |

### 3. 配置层级优先级

```
查询 key 时的查找顺序（高 → 低）：

  ★ Local   (.git/config)
     ↑ 覆盖
  ★ Global  (~/.gitconfig 或 ~/.config/git/config)
     ↑ 覆盖
  ★ System  (/etc/gitconfig 或 C:\Program Files\Git\etc\gitconfig)
```

### 4. 身份覆盖机制（重要）

一个常见场景：全局使用个人身份，特定仓库使用公司身份。

```bash
# 全局：个人身份
git config --global user.name "Personal Name"
git config --global user.email "personal@gmail.com"

# 某仓库内：覆盖为公司身份
git config user.name "Work Name"
git config user.email "work@company.com"
```

此时该仓库中所有 commit 的署名都是 **Work Name <work@company.com>**，其他仓库不受影响。验证当前仓库实际生效的身份：

```bash
git config user.name      # 显示当前仓库实际使用的用户名
git config user.email     # 显示当前仓库实际使用的邮箱
```

### 5. Windows 环境特有配置

这些配置项仅在 Git for Windows 中出现，Linux/macOS 上看不到：

| 配置项 | 作用 |
|--------|------|
| `core.autocrlf=true` | 自动转换换行符，防止 Windows CRLF 与 Unix LF 互相污染 |
| `core.fscache=true` | Windows 文件系统缓存加速 |
| `core.symlinks=false` | 贴合 Windows 无原生符号链接的限制 |
| `core.ignorecase=true` | 贴合 Windows/macOS 大小写不敏感的文件系统 |
| `http.sslbackend=schannel` | 使用 Windows 原生证书存储做 HTTPS 验证 |
| `credential.helper=manager` | 使用 Windows 凭据管理器（凭据不会以明文存储） |

### 6. `.git/config` 文件示例

`git config --local --list` 的输出本质上就是当前仓库的 `.git/config` 文件内容：

```ini
[core]
    repositoryformatversion = 0
    filemode = false
    bare = false
    logallrefupdates = true
    symlinks = false
    ignorecase = true

[user]
    name = repo-specific-name
    email = repo-specific@email.com
```

该文件在 `git init` 时自动生成，`[user]` 段则通过 `git config user.name` / `git config user.email` 手动设置。

---

## 九、配置 SSH 密钥实现免密推送到 GitHub

使用 SSH 密钥认证后，`git push` 不再需要每次输入用户名和密码，安全性也更高。

### 1. 完整的配置步骤

#### 第一步：生成 SSH 密钥对

```bash
# 使用 Ed25519 算法（推荐，更安全更快）
ssh-keygen -t ed25519 -C "your_email@example.com"

# 如果你的系统不支持 Ed25519（较老版本），用 RSA 替代：
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

执行后会看到三个提示：

```
Enter file in which to save the key (/home/you/.ssh/id_ed25519):
# ↑ 直接回车，使用默认路径

Enter passphrase:
# ↑ 输入密码短语（可以为空直接回车，但建议设置）
# 设置后可获得额外安全性：私钥被盗也无法直接使用

Enter same passphrase again:
# ↑ 重复输入
```

生成的文件：

```
~/.ssh/
├── id_ed25519        # 私钥（绝不可公开！）
└── id_ed25519.pub    # 公钥（可以安全地给任何人/服务）
```

#### 第二步：启动 ssh-agent 并添加私钥

```bash
# 启动 ssh-agent（后台进程，帮你管理私钥的解锁状态）
eval "$(ssh-agent -s)"

# 将私钥添加到 ssh-agent（如果设置了 passphrase，这里需要输入一次）
ssh-add ~/.ssh/id_ed25519
```

#### 第三步：将公钥添加到 GitHub

```bash
# 复制公钥内容
cat ~/.ssh/id_ed25519.pub
```

然后在 GitHub 网页上操作：

1. 打开 [github.com](https://github.com) → 右上角头像 → **Settings**
2. 左侧菜单 → **SSH and GPG keys**
3. 点击绿色按钮 **New SSH key**
4. **Title**：填一个辨识度高的名称（如 "My Windows PC"）
5. **Key type**：选择 `Authentication Key`
6. **Key**：粘贴刚才复制的公钥内容（以 `ssh-ed25519` 开头，邮箱结尾的一长串）
7. 点击 **Add SSH key**

#### 第四步：测试连接

```bash
ssh -T git@github.com
```

成功显示：

```
Hi <你的GitHub用户名>! You've authenticated, but GitHub does not provide shell access.
```

> ⚠️ 第一次连接会弹出主机验证提示：
> ```
> The authenticity of host 'github.com (20.205.243.166)' can't be established.
> ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
> Are you sure you want to continue connecting (yes/no)?
> ```
> 输入 `yes` 回车即可。

#### 第五步：将本地仓库远程地址改为 SSH 格式

```bash
# 查看当前远程地址
git remote -v

# 如果是 HTTPS 格式，改为 SSH 格式
git remote set-url origin git@github.com:你的用户名/你的仓库名.git

# 验证
git remote -v
# origin  git@github.com:你的用户名/你的仓库名.git (fetch)
# origin  git@github.com:你的用户名/你的仓库名.git (push)
```

#### 第六步：测试推送

```bash
git add .
git commit -m "test ssh push"
git push
# 不再提示输入用户名和密码，推送直接完成 ✅
```

### 2. 工作原理

```
┌──────────────────────┐                      ┌──────────────────────┐
│      你的电脑         │   ① ssh-keygen        │       GitHub          │
│                      │ ───────────────────→ │                      │
│  私钥（本地保存）     │    生成公钥 + 私钥     │  收到公钥             │
│  绝不外传             │                      │  关联到你的账户        │
│                      │   ② 通过网页上传公钥   │                      │
│                      │ ───────────────────→ │                      │
└──────────┬───────────┘                      └──────────┬───────────┘
           │                                             │
           │  ③ git push 时，本地用私钥签名                │
           │ ────────────────────────────────────────────→ │
           │                                             │
           │  ④ GitHub 用公钥验证签名，通过后接受推送       │
           │ ←──────────────────────────────────────────── │
           │                                             │
           │  全程无需密码，公私密钥对完成身份验证 ✅          │
```

**核心原理**：公钥加密、私钥解密。GitHub 用你上传的公钥加密一段随机消息发给你的电脑，你的电脑只有用配对的那个私钥才能解密并正确回应，从而证明"我就是私钥持有者"。整个过程私钥从不离开你的电脑。

### 3. 常见问题

| 问题 | 解决方法 |
|------|----------|
| 每次开机都要重新 `ssh-add`？ | Windows 可以将 `ssh-add ~/.ssh/id_ed25519` 加入 `~/.bashrc` 或 `~/.profile`；macOS 用 `ssh-add --apple-use-keychain ~/.ssh/id_ed25519` |
| 多台电脑怎么办？ | **每台电脑生成各自的密钥对**，分别添加到 GitHub。不要在电脑间复制私钥 |
| 私钥泄漏了怎么办？ | 立即在 GitHub → Settings → SSH keys 中删除对应公钥，本地重新生成 |
| 仍然 `Permission denied`？ | 用 `ssh -Tv git@github.com` 查看详细调试输出，常见原因：公钥未正确粘贴、使用了错误的 GitHub 用户名 |
| 公司/个人两套身份？ | 可以用 `~/.ssh/config` 配置 Host 别名，不同的 Host 用不同的密钥 |

### 4. HTTPS Token 方式（备选方案）

如果不想用 SSH，也可以用 HTTPS + Personal Access Token：

```bash
# 1. GitHub → Settings → Developer settings → Personal access tokens → Generate new token
# 2. 勾选 repo 权限，生成 token
# 3. 使用 token 作为密码
git remote set-url origin https://github.com/你的用户名/你的仓库名.git
git push
# Username: 你的用户名
# Password: 粘贴 token（不是 GitHub 登录密码！）
```

但 SSH 密钥方式依然是**更推荐**的做法：配置一次，永久免密，且更安全。

---

## 十、大型多人协作项目管理

### 1. 三大核心原则

```
                稳定性
                  ▲
                /  \
               /    \
              /      \
             /  项目  \
            /   管理   \
           /            \
          /              \
         ─────────────────
        可追溯性          可并行性
```

| 原则 | 含义 | 对应机制 |
|------|------|----------|
| **稳定性** | 主分支始终处于可发布状态，任何时候 `git clone` 下来都能跑 | 分支保护规则、CI 门禁 |
| **可追溯性** | 每一行代码都能追溯到"谁、什么时候、为什么"修改的 | commit 规范、Pull Request、Issue 关联 |
| **可并行性** | 多人同时开发不同功能互不阻塞 | 合理的分支策略、频繁合并 |

---

### 2. Git 分支策略（Branching Model）

#### 2.1 Git Flow（最经典，适合有固定发布周期的项目）

```
master (main)  ●────────── ●────────────────────── ●──────── ●─   ← 每个 tag 对应一个发布版本
                │            │                        │
hotfix/        │            │           ●────────────┘           ← 线上紧急修复
                │            │
release/       │       ●────┴────●                              ← 发布前的测试冻结期
                │      /         /
develop       ●─┴──●───●────●───●──●────●──                     ← 日常开发主线
               │   /    /    /
feature/      ●──●     ●──●                                     ← 每个新功能一个分支
```

| 分支类型 | 用途 | 从哪分出 | 合并到哪 |
|----------|------|----------|----------|
| `main` / `master` | 生产环境代码，每个 commit 都应该可发布 | — | — |
| `develop` | 开发主线，集成所有功能 | `main` | `main`（通过 release） |
| `feature/*` | 单个新功能开发 | `develop` | `develop` |
| `release/*` | 发布准备（修 bug、改版本号） | `develop` | `main` + `develop` |
| `hotfix/*` | 线上紧急修复 | `main` | `main` + `develop` |

#### 2.2 GitHub Flow（更简单，适合持续部署）

```
main  ●────────●────────●────────●──→  始终可部署
      │        │        │
      │   ●────┘        │              ← feature A 通过 PR 合并
      │        │   ●────┘              ← feature B 通过 PR 合并
      │
      ●──●──●                           ← feature C 在分支上开发，PR 审查后合并
```

核心规则只有一条：**`main` 分支永远可部署**。所有新功能通过分支 + Pull Request 完成。

#### 2.3 Trunk-Based Development（适合大厂、高频发布）

```
main  ●─●─●─●─●─●─●─●─●─●─→  每天多次提交到主干
      │   │   │
      ●   ●   ●                  ← 极短生命周期的功能分支（< 1 天）
```

分支存活不超过 1 天，通过 Feature Flag 控制未完成功能的可见性。Google、Facebook 等大厂广泛使用。

#### 2.4 三种策略对比

| 对比维度 | Git Flow | GitHub Flow | Trunk-Based |
|----------|----------|-------------|-------------|
| **复杂度** | 高 | 低 | 极低 |
| **分支种类** | 5 种 | 2 种（main + feature） | 1 种（main） |
| **发布频率** | 数周一次 | 按需（可能一天多次） | 一天数十次 |
| **适合团队** | 有固定发布周期的传统软件 | SaaS / Web 应用 | 大型互联网公司 |
| **CI/CD 要求** | 低 | 中 | 极高 |

---

### 3. Pull Request（PR）工作流详解

PR 是 GitHub 对 Git 最重要的贡献，也是多人协作的核心载体。它本质上是一个**被美化的合并请求**——在真正合并代码之前，提供一个讨论、审查、自动化检查的平台。

#### 3.1 PR 的本质

一个 PR 包含三样东西：

```
┌─────────────────────────────────────────────────────────┐
│                     Pull Request                         │
│                                                         │
│  ① 代码差异（Diff）                                      │
│     └── 从 feature 分支相对于 main 分支的所有改动         │
│                                                         │
│  ② 对话记录（Conversation）                              │
│     └── 审查评论、修改讨论、决策记录，不可变的历史档案      │
│                                                         │
│  ③ 自动化检查结果（Status Checks）                       │
│     └── CI 测试、Lint、构建、安全扫描 是否通过            │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

#### 3.2 PR 生命周期

```
 阶段 1: 创建          阶段 2: 审查            阶段 3: 合并           阶段 4: 清理
┌────────────┐      ┌────────────┐         ┌────────────┐        ┌────────────┐
│            │      │            │         │            │        │            │
│  推送分支   │ ───→ │  CI 检查   │ ───→    │  代码审查   │ ───→   │  合并到     │ ───→ │  删除分支   │
│  创建 PR   │      │  自动运行  │         │  人工审查   │        │  main      │      │            │
│            │      │            │         │            │        │            │      │            │
└────────────┘      └────────────┘         └────────────┘        └────────────┘      └────────────┘
                           │                      │
                    失败 ↓ │              修改要求 ↓
                    ┌────────────┐        ┌────────────┐
                    │  修复代码   │        │  修改代码   │
                    │  重新推送   │        │  回复评论   │
                    └────────────┘        └────────────┘
                           │                      │
                           └──────────┬───────────┘
                                      │
                                      ↓
                         回到阶段 2（循环直到 CI 通过 + 审查批准）
```

#### 3.3 PR 的完整操作流程

##### 创建 PR 之前

```bash
# 1. 确保本地 main 是最新的
git checkout main
git pull origin main

# 2. 从 main 创建功能分支（命名要有意义）
git checkout -b feature/user-avatar-upload
#   命名规范：<type>/<description>
#   例子：feature/login-oauth, fix/payment-timeout, refactor/db-pool

# 3. 开发，小步提交
git add src/upload.js
git commit -m "feat(upload): add multipart file upload handler"

git add src/upload.test.js
git commit -m "test(upload): add unit tests for file size validation"

git add src/frontend/avatar.jsx
git commit -m "feat(avatar): add avatar preview component"

# 4. 推送分支到 GitHub
git push origin feature/user-avatar-upload
```

##### 在 GitHub 上创建 PR

```
方式 A：git push 后在终端输出的链接直接打开
  remote: Create a pull request for 'feature/user-avatar-upload' on GitHub by visiting:
  remote: https://github.com/user/repo/pull/new/feature/user-avatar-upload

方式 B：在 GitHub 仓库页面上手动操作
  1. 打开仓库页面，点击 "Pull requests" 标签
  2. 点击绿色按钮 "New pull request"
  3. base 选择 main ← compare 选择 feature/user-avatar-upload
  4. 点击 "Create pull request"

填写 PR 描述：

```
┌───────────────────────────────────────────────────────────────┐
│ Title: feat(avatar): add user avatar upload                  │
│                                                              │
│ Description:                                                 │
│   ## What                                                    │
│   - 添加用户头像上传功能                                       │
│   - 支持 JPG/PNG/GIF 格式                                    │
│   - 最大 2MB                                                 │
│                                                              │
│   ## Screenshots                                             │
│   (拖入截图)                                                  │
│                                                              │
│   ## Related Issue                                           │
│   Closes #42                                                 │
│                                                              │
│   ## Testing                                                 │
│   - [ ] 单元测试通过                                          │
│   - [ ] 手动测试过大文件被拒绝                                 │
│                                                              │
└───────────────────────────────────────────────────────────────┘
```

##### Draft PR（草稿 PR）

如果功能还没做完，但想让同事了解进度，可以创建 Draft PR：

```
在创建 PR 时，点击 "Create pull request" 旁边的下拉箭头
→ 选择 "Create draft pull request"

Draft PR 的特点：
  ✅ 可以看到代码差异和对话
  ✅ 可以触发 CI 运行
  ❌ 不能合并（Merge 按钮灰色不可点击）
  ❌ CODEOWNERS 不会自动请求审查

当功能完成时，点击 "Ready for review" 转为正式 PR
```

##### 代码审查过程

审查者看到的页面：

```
┌── Files changed (3) ──────────────────────────────────────────┐
│                                                               │
│  src/upload.js                                     +45 -2     │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │ 124  function validateFile(file) {                     │  │
│  │ 125    if (file.size > MAX_SIZE) {                     │  │
│  │ +      throw new FileTooLargeError(file.size);         │  │
│  │ +    }                                                 │  │
│  │ 126    return true;                                    │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                               │
│  审查者点击 "+" 号添加行级评论：                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │ 💬 Should we also check file.type here?                 │  │
│  │    Consider validating against an allowlist of          │  │
│  │    MIME types to prevent malicious uploads.             │  │
│  │                                                        │  │
│  │ [Add single comment]    [Start a review]                │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                               │
│  src/upload.test.js                                +32 -0     │
│  src/frontend/avatar.jsx                           +18 -5     │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

审查评论分为三种类型：

| 类型 | 含义 | 何时用 |
|------|------|--------|
| **Comment** | 普通评论，不阻断合并 | 提问、建议、讨论 |
| **Approve** | 批准，允许合并 | 代码没问题 |
| **Request changes** | 要求修改，**阻断合并** | 有 bug、安全问题、不符合规范 |

##### 响应审查意见

```bash
# 1. 根据审查意见修改代码
git checkout feature/user-avatar-upload
# 修改文件...
git add src/upload.js
git commit -m "fix(upload): add MIME type validation per PR review"

# 2. 推送修改（PR 会自动更新）
git push origin feature/user-avatar-upload
```

PR 页面会自动：
- 显示新的 commit
- 标记之前的审查为 "stale"（过时），通知审查者重新审
- 重新触发 CI

##### 合并 PR

审查通过 + CI 全绿后，有三种合并方式：

```
┌─────────────────────────────────────────────────────────┐
│ Merge pull request                                       │
│                                                         │
│  ○ Create a merge commit                               │
│    └── 所有分支 commit 原样保留，产生一个 merge commit    │
│        适合：需要保留完整开发历史的项目                    │
│                                                         │
│  ● Squash and merge               ← GitHub 默认推荐     │
│    └── 所有分支 commit 压缩成 1 个，历史整洁              │
│        适合：小功能、修 bug，多数团队的选择                │
│                                                         │
│  ○ Rebase and merge                                    │
│    └── 分支 commit 逐个 rebase 到 main 顶端              │
│        适合：追求完全线性历史的项目                       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

三种方式的可视化对比：

```
Merge Commit:            Squash & Merge:          Rebase & Merge:
  *  merge commit          *  feat: login           *  feat: login
  |\                       |                        |
  | * feat commit 3        *  feat: payment         *  feat: payment
  | * feat commit 2        |                        |
  | * feat commit 1        *  fix: typo             *  fix: typo
  |/                       |                        |
  *  previous commit        *  previous commit        *  previous commit

  保留开发过程             干净，但丢失细节           完全线性，提交被重写
```

#### 3.4 PR 的最佳实践

**对于 PR 创建者：**

| 实践 | 说明 |
|------|------|
| **PR 越小越好** | 理想情况 < 400 行改动。大 PR 拆分提交，审查者更愿意认真看 |
| **一个 PR 只做一件事** | 不要在一个 PR 中既重构又加新功能又修 bug |
| **写好 PR 描述** | 说清楚做了什么、为什么这样做、如何测试 |
| **关联 Issue** | 描述中写 `Closes #123`，合并后自动关闭对应 Issue |
| **先自查再请求审查** | 自己先在 Files changed 中看一遍 diff，发现明显问题先改 |
| **@mention 合适的审查者** | 不要无差别 @所有人，找最了解这块代码的人 |

**对于审查者：**

| 实践 | 说明 |
|------|------|
| **24 小时内响应** | 不一定要审完，但至少说"我明天看"，不要让人等待 |
| **区分必须改和建议改** | 用 "nit:"（nitpick）标记非必须的建议，用 "must:" 标记必须修改的 |
| **说为什么，而不只是什么** | "这里应该用 const" → "这里用 const 能防止意外的重新赋值，提高可读性" |
| **关注逻辑而非风格** | 格式问题交给 Linter，审查者关注架构、安全、错误处理 |
| **审查是对事不对人** | 用中性语言，如"这个查询可能导致 N+1 问题"而不是"你写的查询太差了" |

#### 3.5 PR 中的常见操作

```bash
# 场景 1：PR 提交后，main 又有新 commit，需要更新你的 PR 分支
git checkout main
git pull origin main
git checkout feature/user-avatar-upload
git merge main                    # merge 方式：产生一个 merge commit
# 或
git rebase main                   # rebase 方式：提交历史更整洁（需要 force push）
git push --force-with-lease origin feature/user-avatar-upload

# 场景 2：审查者要求修改，你不想要太多"fix review"的 commit
# 在本地用 amend 修改上一个 commit，而不是新建一个
git add src/fixed-file.js
git commit --amend --no-edit      # 不改变 commit message，只是追加改动
git push --force-with-lease origin feature/user-avatar-upload

# 场景 3：你的分支包含多个杂乱的小 commit，想整理干净
git rebase -i main                # 交互式 rebase
# 在弹出的编辑器中，将后面的 commit 标记为 squash/fixup
# 让它们合并到第一个 commit 中，形成一个干净的 PR
git push --force-with-lease origin feature/user-avatar-upload
```

#### 3.6 PR 描述模板示例

在项目根目录创建 `.github/pull_request_template.md`：

```markdown
## 📝 描述

请简要描述这个 PR 做了什么。

## 🔗 关联 Issue

Closes #

## 📸 截图

（如果有 UI 变更，请附上截图）

## ✅ 自查清单

- [ ] 代码通过本地测试
- [ ] 添加了必要的单元测试
- [ ] 更新了相关文档
- [ ] 没有引入新的 Lint 警告

## 🧪 如何测试

1. 切换到该分支
2. 运行 `npm test`
3. 检查 `xxx` 功能是否正常
```

---

### 4. Commit 规范

好的 commit 信息是可追溯性的基础。推荐 **Conventional Commits** 规范：

```
<type>(<scope>): <subject>

<body>

<footer>
```

| type | 含义 | 示例 |
|------|------|------|
| `feat` | 新功能 | `feat(auth): add login with Google OAuth` |
| `fix` | 修复 bug | `fix(api): handle null response from payment gateway` |
| `refactor` | 重构（不改变功能） | `refactor(db): extract connection pool to shared module` |
| `docs` | 文档 | `docs(readme): add deployment instructions` |
| `test` | 测试 | `test(login): add unit tests for password validation` |
| `chore` | 杂务（依赖更新等） | `chore(deps): bump express to 4.18.0` |

#### commit 粒度原则

```
好的 commit：                         不好的 commit：
一个 commit = 一个逻辑变更             一个 commit = 一天的工作
                                      ("WIP"、"fix stuff"、"update")

git log 读起来像一本书的目录            git log 读起来像一团乱麻
```

---

### 5. 分支保护规则（Branch Protection Rules）

对 `main` 分支应该设置（GitHub Settings → Branches → Add rule）：

```
main 分支保护规则：

✅ Require a pull request before merging
   └── ✅ Require approvals (至少 1 人批准)
   └── ✅ Dismiss stale approvals when new commits are pushed
       （有人 push 新 commit 后，之前的批准作废，必须重新审查）

✅ Require status checks to pass before merging
   └── ✅ CI tests
   └── ✅ Lint check
   └── ✅ Build check

✅ Require conversation resolution before merging
   （所有审查评论必须解决才能合并）

✅ Require branches to be up to date before merging
   （合并前必须先与最新的 main 同步，避免合并后 main 出现意外问题）

❌ Do not allow bypassing the above settings
   （即使是管理员也不能跳过这些规则）
```

---

### 6. 合并冲突的预防与解决

#### 预防冲突

```bash
# 好习惯：每天从 main 拉取最新代码
git checkout main
git pull origin main
git checkout feature/my-branch
git merge main          # 频繁小合并，冲突少且容易解决
```

#### 解决冲突

```bash
# 当 git merge 提示冲突时：
# Auto-merging src/file.js
# CONFLICT (content): Merge conflict in src/file.js

# 1. 查看冲突文件
git status                # 查看哪些文件有冲突

# 2. 打开冲突文件，内容类似：
# <<<<<<< HEAD
# const port = 3000;
# =======
# const port = process.env.PORT || 3000;
# >>>>>>> feature/add-env-config

# 3. 手动编辑，选择保留的内容（删除标记线），改为：
# const port = process.env.PORT || 3000;

# 4. 标记为已解决
git add src/file.js

# 5. 完成合并
git commit               # Git 会自动生成合并 commit message
```

---

### 7. 实际团队运作的一天

```
早上 9:00
  git checkout main
  git pull
  git checkout -b feature/user-avatar

上午 11:30
  git add .
  git commit -m "feat(avatar): add upload endpoint"
  git push origin feature/user-avatar
  # 创建 Draft PR（让同事知道你在做什么，但不能合并）

下午 3:00
  git add .
  git commit -m "feat(avatar): add frontend preview component"
  git push
  # Draft PR → Ready for Review
  # 在 GitHub 上 @同事 请求审查

下午 4:00（同事审完后有修改意见）
  # 修改代码...
  git add .
  git commit -m "fix(avatar): handle file size validation per review"
  git push
  # PR 页面自动更新，通知同事重新审查

下午 5:00（审批通过，CI 全绿）
  # 点击 GitHub 上的 "Squash and merge"
  # 删除远程分支（GitHub 自动提供按钮）
  git checkout main
  git pull
  git branch -d feature/user-avatar
```

---

### 8. 核心准则总结

```
1. main 分支永远可部署
   └── 任何人不直接 push 到 main，必须通过 PR

2. 一个分支只做一件事
   └── 功能分支 / 修复分支 / 重构分支严格分离

3. 小步提交，频繁合并
   └── PR 越小越好审，每次合并越安全

4. Commit 信息是你的"代码日记"
   └── 6 个月后回来看，也能看懂每个 commit 做了什么

5. 代码审查是知识共享，不是找茬
   └── 审的是代码质量、安全性、可维护性，不是个人

6. CI 不过就不能合并
   └── 自动化测试是最后一道防线，人工审查不能替代

7. 团队协作 = 信任 + 流程
   └── 流程不是束缚，是保护每个人不犯低级错误的安全网
```
