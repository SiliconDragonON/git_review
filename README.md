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
工作目录          暂存区            本地仓库
(Working         (Staging          (Local
Directory)       Area)             Repository)
    │               │                  │
    │   git add     │   git commit     │
    │ ───────────→  │ ───────────────→ │
    │               │                  │
    │   git checkout / git restore     │
    │ ←────────────────────────────────│
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
┌────────────────────────────┐
│ blob A (10MB) — 作为基准    │  ← 完整存储，必须保留
│ blob B (1KB)  — 对 A 的 delta │  ← 只存差异
└────────────────────────────┘
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
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   仓库总大小 = 当前快照 + 整个历史链上的所有旧快照        │
│                                                         │
│   文件变小了，仓库不会变小——旧的 10MB 永存于历史中！      │
│                                                         │
└─────────────────────────────────────────────────────────┘
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

| | **Git** | **GitHub** |
|---|---|---|
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
本地仓库                              GitHub 远程仓库
┌───────────────┐                   ┌──────────────────┐
│ commit C1     │ ─── git push ──→  │ commit C1        │
│ commit C2     │                   │ commit C2        │
│ commit C3     │                   │ commit C3        │
│ refs/heads/   │                   │ refs/heads/main  │
│   main → C3   │                   │   → C3           │
└───────────────┘                   └──────────────────┘

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

| | **SSH** | **HTTPS** |
|---|---|---|
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
