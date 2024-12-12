---
title: Git基础
date: 2024-12-12 17:44:16
categories: 学习笔记
tags: [git]  
cover: https://img2.huashi6.com/images/resource/2022/04/28/h97937064p0.jpg?imageMogr2/quality/75/interlace/1/thumbnail/1400x/gravity/North/crop/1400x1570/format/webp
---

### 关于版本控制

**什么是版本控制？**
 版本控制是一种系统，用于记录一个或多个文件的内容变化，以便将来查阅特定版本的修订情况。

**为什么需要版本控制？**

- **适用范围广**：不仅限于软件源代码，还适用于设计图、网页布局等各种文件类型。
- 核心功能：
  - 回溯文件到之前的状态。
  - 回退整个项目到某个时间点。
  - 比较文件改动细节。
  - 查明修改人和修改时间。
  - 追踪问题原因（如功能缺陷、错误引入点）。
- **恢复保障**：即使误操作修改或删除文件，也能轻松恢复原状。
- **高效成本**：引入版本控制后，额外工作量几乎可以忽略不计，却能显著提升管理和协作效率。

版本控制系统（VCS）是处理上述需求的明智选择，它让文件和项目管理更高效、更可控。

**版本控制系统的发展与特点**

1. **本地版本控制系统**

- **传统方式的问题**：直接复制项目文件夹并标记时间的方式简单但容易出错，比如覆盖文件或修改错误目录。
- 解决方案：
  - **RCS (Revision Control System)**：早期本地版本控制工具，通过保存“补丁集”（文件修改的差异）在硬盘中重建各版本内容。
- **缺点**：所有历史记录集中存储，存在单点故障风险，容易丢失数据。

<img src="https://p.ipic.vip/bcow89.png" alt="本地版本控制图解" style="zoom:50%;" />

2. **集中化版本控制系统 (CVCS)**

- **目的**：解决分散开发者协作的问题。

- 特点：

  - 中心服务器存储所有文件修订版本，开发者通过客户端提取和提交更新。
  - 代表工具：CVS、Subversion (SVN)、Perforce。
  - 优点：
    - 团队成员可实时了解他人进展。
    - 管理员轻松管理权限，维护方便。
  - 缺点：
    - 单点故障风险：
      - 服务器宕机时，协作停止。
      - 数据库损坏且无备份时，历史记录丢失。

  <img src="https://git-scm.com/book/zh/v2/images/centralized.png" alt="集中化的版本控制图解" style="zoom:50%;" />

3. **分布式版本控制系统 (DVCS)**

- **核心改进**：每个客户端不仅获取文件快照，还完整镜像代码仓库，包括所有历史记录。
- 特点：
  - 数据冗余：任一本地仓库都可用于恢复服务器数据。
  - 多远端仓库协作：支持多样化的工作流（如层次模型）。
  - 代表工具：Git、Mercurial、Darcs。
- 优点：
  - 无单点故障：任一仓库损坏都可从其他仓库恢复。
  - 灵活协作：支持与多个团队并行工作，实现复杂的协作流程。

<img src="https://p.ipic.vip/acbclw.png" alt="分布式版本控制图解" style="zoom: 33%;" />

### **Git 是什么？——核心思想与特点**

#### **1. Git 的核心理念**

Git 是一个以 **快照流** 为核心设计的版本控制系统，区别于传统的差异存储（如 CVS 和 Subversion）。

- 快照存储：Git 记录的是文件系统的完整快照，每次提交都会保存当前文件的状态。

  - 未修改的文件通过引用指向上次存储的内容，而非重复存储。

  <img src="https://p.ipic.vip/t1dqm2.png" alt="Git 存储项目随时间改变的快照。" style="zoom: 33%;" />

- **结果**：操作高效、历史清晰、恢复便捷。

**示例**：
 假设你修改了一个文件并提交，Git 会创建一个完整快照并保存变更，而非仅记录变化。若文件未改动，仅存储指针以节省空间。

假设你每天给家里的书架拍一张照片来记录变化：

- **其他版本控制系统** 会只拍摄新添加或移动的书，并记下“差异”。
- **Git** 则拍摄书架的完整照片，但如果书架没有变化，它会直接用之前的照片，而不是重新拍摄。

这样，回顾某一天的状态时：

- 基于差异的系统需要对比所有照片的差异来重建书架。
- Git 可以直接拿出当天完整的照片查看书架的样子。

------

#### **2. 本地操作为主**

Git 的绝大多数操作都是本地完成的，依赖本地仓库完整的历史数据。

- **高效性**：无需网络延时，操作如浏览历史、查看差异几乎瞬时完成。
- **离线支持**：即使无网络也可提交、查看记录并继续工作，后续再同步远程仓库。

**对比**：集中式系统（如 Perforce、CVS）依赖服务器，离线状态下无法提交或访问完整历史，而 Git 可以离线执行大多数操作。

------

#### **3. 数据完整性与安全性**

- SHA-1 校验和：Git 为每个数据块生成唯一的 40 位哈希值，确保文件内容不能在不被发现的情况下更改。
  - 示例哈希：`24b9da6552252987aa493b52f8696cd6d3b00373`
- **不可逆删除**：Git 通常只添加数据，已提交的内容几乎不可丢失。即使误操作，只需从其他备份仓库恢复。

------

#### **4. 三种状态与三个区域**

Git 文件有三种状态：

- **已修改**（modified）：文件已更改，但未保存到数据库中。
- **已暂存**（staged）：文件修改后被标记，将包含在下次提交的快照中。
- **已提交**（committed）：文件已保存到 Git 的本地数据库中。

对应的 Git 区域：

1. **工作区**（Working Directory）：提取出的项目文件，供修改和编辑。
2. **暂存区**（Staging Area）：保存即将提交的文件列表信息。
3. **Git 仓库目录**（Repository）：存储元数据和对象数据库，是项目核心。

<img src="https://p.ipic.vip/mduyy2.png" alt="工作区、暂存区以及 Git 目录。" style="zoom:33%;" />

**基本流程**：

1. 修改文件（工作区）。
2. 将修改暂存（暂存区）。
3. 提交快照（Git 仓库目录）。

**示例**：
 修改文件后用 `git add` 暂存到索引中，再通过 `git commit` 提交保存到仓库中。

------

### **命令行模式**

Git 提供命令行和 GUI 两种使用方式。git建议选择 **命令行模式**，原因如下：

1. **功能完整性**：命令行支持 Git 的所有功能，而大多数 GUI 工具仅支持部分功能。
2. **通用性**：掌握命令行后，无论使用何种 GUI，都能轻松上手。
3. **一致性**：每个用户都可访问命令行，而 GUI 工具因需求不同可能有所差异。

------

### **macOS Terminal 常用命令**

以下是常用的 Terminal 命令，便于学习 Git 和管理系统：

#### **1.文件与目录操作**

- `pwd`：显示当前所在路径。
- `ls`：列出当前目录下的文件和文件夹。
  - 示例：`ls -la` 显示隐藏文件及详细信息。
- `cd [路径]`：切换目录。
  - 示例：`cd ~/Documents` 切换到用户文档目录。
- `mkdir [目录名]`：创建新目录。
- `rm [文件名]`：删除文件。
  - 示例：`rm -rf [目录名]` 删除目录及其内容。

#### 2.**文件操作**

- `touch [文件名]`：创建空文件。
- `cat [文件名]`：查看文件内容。
- `cp [源文件] [目标路径]`：复制文件。
- `mv [源文件] [目标路径]`：移动或重命名文件。

#### 3.**系统与进程管理**

- `top`：查看当前系统运行状态和进程。
- `ps -ax`：列出当前所有进程。
- `kill [进程号]`：终止指定进程。

#### 4.**软件包管理**（需安装 Homebrew）

- `brew install [软件名]`：安装软件。
- `brew uninstall [软件名]`：卸载软件。
- `brew update`：更新 Homebrew 和已安装包的信息。

#### 5.**Git 相关**

- `git init`：初始化 Git 仓库。
- `git status`：查看仓库当前状态。
- `git add [文件名]`：将文件添加到暂存区。
- `git commit -m "[信息]"`：提交更新。
- `git log`：查看提交历史。

#### 6.**快捷操作**

- `clear`：清屏，清除终端当前显示内容。
- `exit`：关闭终端会话。

### Mac 上安装与配置 Git 

------

#### **安装 Git**

1. **通过 Homebrew 安装 Git**
   在终端执行以下命令：

   ```bash
   brew install git
   ```

2. **验证安装**
   确保安装成功并查看版本：

   ```bash
   git --version
   ```

3. **设置自动更新（可选）**
   确保通过 Homebrew 定期更新 Git：

   ```bash
   brew update
   brew upgrade git
   ```

------

#### **初次运行 Git 前的配置**

Git 的配置由 `git config` 管理，可通过以下位置定义：

1. 系统级配置：

   ```
   /etc/gitconfig
   ```

   - 对系统所有用户生效。使用 `--system` 选项修改。

2. 用户级配置：

   ```
   ~/.gitconfig
   ```

    或 

   ```
   ~/.config/git/config
   ```

   - 当前用户生效，使用 `--global` 选项修改。

3. 仓库级配置：项目目录下的 

   ```
   .git/config
   ```

   - 仅对当前仓库生效，使用 `--local` 修改（默认行为）。

配置项的优先级：仓库级 > 用户级 > 系统级。

查看所有配置：

```bash
git config --list --show-origin
```

------

#### **配置用户信息**

1. 配置用户名和邮箱：

   ```bash
   git config --global user.name "408MyDad"
   git config --global user.email "youremail@example.com"
   ```

   > 这会全局生效，如需针对某项目单独设置，去掉 `--global`，在项目目录下执行。

2. 检查用户配置：

   ```bash
   git config user.name
   git config user.email
   ```

------

#### **设置默认文本编辑器**

Git 需要一个文本编辑器来输入提交信息等内容。默认会使用系统的编辑器，推荐更换为常用的编辑器。

1. **使用 Neovim**：

   ```bash
   git config --global core.editor "nvim"
   ```

2. **测试编辑器设置**：
   执行需要编辑器的命令，如 `git commit`，确保 Neovim 能正常启动。

3. **验证编辑器配置**：

   ```bash
   git config core.editor
   ```

   应返回 `nvim`。

------

#### **检查和修改 Git 配置**

1. **查看当前所有配置**：

   ```bash
   git config --list --show-origin
   ```

   > 显示所有配置及其定义文件。

2. **查看特定配置项**：

   ```bash
   git config user.name
   ```

3. **查询配置项来源**：

   ```bash
   git config --show-origin user.name
   ```

   > 返回配置值及定义的文件路径。

------

### Git 基础 - 获取 Git 仓库

#### 1. 两种获取方式

- **初始化本地目录为 Git 仓库**：将已有目录转换为 Git 仓库。
- **克隆远程仓库**：从其他服务器复制完整的 Git 仓库。

------

#### 2. 在本地目录中初始化仓库

1. 进入项目目录（以 macOS 为例）：

   ```bash
   cd /Users/user/my_project
   ```

2. 初始化 Git 仓库：

   ```bash
   git init
   ```

   - 创建 `.git` 子目录，包含所有必要文件。

3. 开始跟踪文件并提交：

   ```bash
   git add *.c LICENSE
   git commit -m "Initial project version"
   ```

------

#### 3. 克隆远程仓库

1. 克隆仓库到当前目录：

   ```bash
   git clone https://github.com/libgit2/libgit2
   ```

   - 创建 `libgit2` 文件夹，初始化 `.git` 子目录，下载所有数据并准备好文件。

2. 自定义本地目录名：

   ```bash
   git clone https://github.com/libgit2/libgit2 mylibgit
   ```

   - 将远程仓库克隆到 `mylibgit` 文件夹。

------

#### 4. 支持的传输协议

Git 支持多种协议，如：

- `https://`
- `git://`
- SSH（`user@server:path/to/repo.git`）

选择适合的协议完成仓库克隆即可。

###  Git 基础 - 记录每次更新到仓库

在 Git 中，记录每次更新到仓库的流程分为多个步骤，主要涉及对文件的状态管理、暂存、提交等操作。以下是基本流程和相关命令的详细说明。

#### 1. 文件状态

在 Git 中，文件有两种状态：**已跟踪**（Tracked）和**未跟踪**（Untracked）。

- **已跟踪文件**：这些文件在 Git 仓库中已经有记录，它们的状态可能是：未修改（Unmodified）、已修改（Modified）或已暂存（Staged）。
- **未跟踪文件**：这些文件尚未被 Git 跟踪，即 Git 之前没有记录这些文件。通常是新创建的文件，Git 不会自动跟踪，除非明确使用 `git add` 命令添加。

![Git 下文件生命周期图。](https://p.ipic.vip/8z50mb.png)

#### 2. 查看当前文件状态

使用 `git status` 命令查看工作区的文件状态。例如，克隆一个仓库后，如果没有修改任何文件，执行 `git status` 将看到类似以下信息：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

这表示当前工作区没有未跟踪文件或未提交的更改。创建新文件（如 `README`）并查看状态时，会看到未跟踪文件：

```
$ echo 'My Project' > README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README
nothing added to commit but untracked files present (use "git add" to track)
```

#### 3. 跟踪新文件

使用 `git add` 命令将未跟踪的文件添加到 Git 的暂存区。例如：

```
$ git add README
```

此时再次使用 `git status` 查看，`README` 文件已经被暂存，准备提交：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

    new file:   README
```

#### 4. 暂存已修改的文件

修改已跟踪的文件时，使用 `git status` 查看状态：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

要将修改的文件添加到暂存区，使用 `git add`：

```
$ git add CONTRIBUTING.md
```

再次执行 `git status` 查看文件状态：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```

#### 5. 状态简览

使用 `git status -s` 命令可以得到简洁的文件状态报告。例如：

```
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

- `??` 表示未跟踪的文件。
- `A` 表示已添加到暂存区的文件。
- `M` 表示已修改的文件。

#### 6. 忽略文件

有时我们需要忽略一些不需要 Git 跟踪的文件，比如编译过程中生成的临时文件。可以创建一个 `.gitignore` 文件来指定需要忽略的文件。`.gitignore` 文件使用 glob 模式来匹配需要忽略的文件或目录。

例如：

```
# 忽略所有的 .a 文件
*.a

# 忽略所有临时文件
*~
```

#### 7. 查看文件的差异

使用 `git diff` 查看文件的差异。如果有修改未暂存，可以通过 `git diff` 查看更改的具体内容：

```
$ git diff
```

要查看已暂存的差异，可以使用 `git diff --staged`：

```
$ git diff --staged
```

#### 8. 提交更新

使用 `git commit` 提交已暂存的更改。提交时可以添加消息，描述这次更改的目的。例如：

```
$ git commit -m "Fixed issue with CONTRIBUTING.md"
```

提交后，Git 会保存当前暂存区的快照，并将更改记录在历史中。提交后输出会告诉你本次提交的 SHA-1 校验和，以及有多少文件和行被修改：

```
[master 463dc4f] Fixed issue with CONTRIBUTING.md
 2 files changed, 2 insertions(+), 1 deletion(-)
```

#### 9. 跳过暂存区

有时，如果你想要跳过暂存区直接提交更改，可以使用 `-a` 选项，这会自动将所有已跟踪的文件暂存并提交：

```
$ git commit -a -m "Updated benchmarks"
```

#### 10. 移除文件

如果需要从 Git 仓库中删除某个文件，可以使用 `git rm` 命令。该命令不仅从 Git 中移除文件，还会将文件从工作目录删除：

```
$ git rm CONTRIBUTING.md
$ git commit -m "Removed CONTRIBUTING.md"
```

通过这些基本的 Git 操作，您可以有效地管理项目中的版本变更，跟踪文件的状态，并确保所有重要的更新都被记录下来。

### Git 基础 - 查看提交历史

Git 提供强大的 `git log` 命令用于查看项目的提交历史，能够通过多种选项灵活展示信息。

#### 基本使用

- 默认情况下，`git log`按时间倒序列出提交历史，包含：
  - 提交的 SHA-1 哈希值
  - 作者信息
  - 提交时间
  - 提交说明

#### 常用选项

1. **显示详细信息**：

   - `-p` 或 `--patch`：显示每次提交的改动细节（补丁格式）。
   - `--stat`：显示文件修改的统计信息。

2. **简化显示格式**：

   - `--pretty`：定制提交历史格式：

     - `oneline`：每条提交占一行，适合快速浏览。

     - `format`：自定义显示格式，例如：

       ```bash
       git log --pretty=format:"%h - %an, %ar : %s"
       ```

       常用占位符包括：

       - `%h`：简短哈希
       - `%an`：作者名字
       - `%ar`：相对时间
       - `%s`：提交说明

3. **分支与合并展示**：

   - `--graph`：用 ASCII 图形展示分支和合并历史。

4. **过滤与限制**：

   - `-<n>`：仅显示最近的 n 条提交。
   - `--since` / `--until`：按时间筛选提交。
   - `--author`：仅显示特定作者的提交。
   - `--grep`：按提交说明关键字筛选。
   - `-S`：仅显示添加或删除指定字符串的提交。
   - `--no-merges`：隐藏合并提交。

5. **路径过滤**：

   - 使用路径限制仅显示相关文件或目录的提交记录，格式：

     ```bash
     git log [options] -- [path]
     ```

#### 实例

查看特定条件的提交，例如：

```bash
git log --pretty="%h - %s" --author='Junio C Hamano' --since="2008-10-01" \
   --before="2008-11-01" --no-merges -- t/
```

过滤出作者是 Junio C Hamano，2008 年 10 月期间对 `t/` 目录的非合并提交。

#### 小提示

- 使用 `--no-merges` 隐藏合并提交，简化历史记录。
- 使用 `--oneline` 快速浏览历史，结合 `--graph` 更直观。

### Git 基础 - 撤消操作

#### 1. 修改最近一次提交

使用 `git commit --amend` 修补提交或更改提交信息：

```bash
$ git commit --amend
```

- 替换上一次提交，历史记录不保留。
- 适用于补充遗漏文件或修正文案。

------

#### 2. 取消暂存文件

将文件从暂存区移回修改状态：

```bash
$ git reset HEAD <file>
```

**示例：**

```bash
$ git reset HEAD CONTRIBUTING.md
```

结果：文件变为“已修改未暂存”状态。

------

#### 3. 丢弃文件修改

恢复文件至上次提交状态：

```bash
$ git checkout -- <file>
```

**示例：**

```bash
$ git checkout -- CONTRIBUTING.md
```

**注意**：此命令会丢弃所有本地修改，谨慎使用。

------

### Git 基础 - 远程仓库的使用

#### 1. 查看远程仓库

- **列出已配置的远程仓库**：

  ```bash
  $ git remote
  ```

  通常会显示 `origin`，即克隆时默认的远程仓库名称。

- **显示远程仓库的详细信息**：

  ```bash
  $ git remote -v
  ```

  列出仓库的 URL，用于拉取 (fetch) 和推送 (push)。

------

#### 2. 添加远程仓库

- **命令格式**：

  ```bash
  $ git remote add <简写名> <远程仓库URL>
  ```

- **示例**：

  ```bash
  $ git remote add pb https://github.com/user/repo.git
  $ git remote -v
  origin	https://github.com/origin/repo.git (fetch)
  origin	https://github.com/origin/repo.git (push)
  pb	    https://github.com/user/repo.git (fetch)
  pb	    https://github.com/user/repo.git (push)
  ```

------

#### 3. 从远程仓库抓取和拉取

- **抓取数据**（只下载不合并）：

  ```bash
  $ git fetch <远程名>
  ```

- **拉取数据**（下载并自动合并到当前分支）：

  ```bash
  $ git pull <远程名> <分支名>
  ```

- **示例**：

  ```bash
  $ git fetch origin
  $ git pull origin master
  ```

------

#### 4. 推送到远程仓库

- **推送数据到指定分支**：

  ```bash
  $ git push <远程名> <分支名>
  ```

- **示例**：

  ```bash
  $ git push origin master
  ```

------

#### 5. 查看远程仓库详细信息

- **命令**：

  ```bash
  $ git remote show <远程名>
  ```

- **示例**：

  ```bash
  $ git remote show origin
  ```

  显示远程仓库的 URL、跟踪的分支、本地与远程的分支映射关系等。

------

#### 6. 重命名或移除远程仓库

- **重命名远程仓库**：

  ```bash
  $ git remote rename <旧名> <新名>
  ```

- **移除远程仓库**：

  ```bash
  $ git remote remove <远程名>
  ```

- **示例**：

  ```bash
  $ git remote rename pb paul
  $ git remote remove paul
  ```

------

### Git基础 - 打标签

Git 提供了两种标签：**轻量标签**和**附注标签**。标签通常用于标记重要的提交点（如版本发布）。以下是关于 Git 标签的核心知识点：

------

#### 1. **列出标签**

- 查看所有标签：

  ```bash
  git tag
  ```

- 按模式查找标签（需 `-l` 或 `--list`选项）：

  ```bash
  git tag -l "v1.8.5*"
  ```

------

#### 2. **创建标签**

**附注标签**（包含详细信息，推荐使用）：

```bash
git tag -a <tagname> -m "Tag message"
```

查看附注标签：

```bash
git show <tagname>
```

**轻量标签**（无额外信息）：

```bash
git tag <tagname>
```

查看轻量标签：

```bash
git show <tagname>
```

**给历史提交打标签**： 指定提交哈希：

```bash
git tag -a <tagname> <commit_hash>
```

------

#### 3. **共享标签**

- 推送单个标签：

  ```bash
  git push origin <tagname>
  ```

- 推送所有本地标签：

  ```bash
  git push origin --tags
  ```

------

#### 4. **删除标签**

**删除本地标签**：

```bash
git tag -d <tagname>
```

**删除远程标签**：

- 方法一：

  ```bash
  git push <remote> :refs/tags/<tagname>
  ```

- 方法二：

  ```bash
  git push <remote> --delete <tagname>
  ```

------

#### 5. **检出标签**

检出某个标签对应的代码：

```bash
git checkout <tagname>
```

注意：这会进入**分离头指针（detached HEAD）**状态。如果需要修改内容并提交，建议创建新分支：

```bash
git checkout -b <new-branch> <tagname>
```

**小贴士 **

- **附注标签**存储更多元信息，推荐用于发布版本等重要节点。
- **轻量标签**更简单，适合临时标记。
- 推送标签时，Git 不区分轻量标签和附注标签，都会被传输。

### Git 基础 - Git 别名

Git 提供了一个强大的技巧，可以简化你的操作体验：**别名**。通过为常用命令设置简短的替代名称，能显著提高工作效率。下面是一些关键知识点和常见用法。

------

#### **设置别名**

你可以通过 `git config` 命令为任意 Git 命令设置别名。以下是一些常见别名示例：

```bash
$ git config --global alias.co checkout   # 将 `checkout` 简化为 `co`
$ git config --global alias.br branch     # 将 `branch` 简化为 `br`
$ git config --global alias.ci commit     # 将 `commit` 简化为 `ci`
$ git config --global alias.st status     # 将 `status` 简化为 `st`
```

设置后，你可以通过以下方式快速执行对应命令：

```bash
$ git ci -m "Initial commit"    # 相当于 git commit -m "Initial commit"
$ git br                        # 相当于 git branch
```

------

#### **添加高级别名**

可以为复杂的 Git 命令或组合命令设置别名，进一步提升操作效率。例如：

1. **取消暂存文件**
    为 `reset HEAD --` 添加别名 `unstage`：

   ```bash
   $ git config --global alias.unstage 'reset HEAD --'
   ```

   现在，以下命令等价：

   ```bash
   $ git unstage <file>    # 更直观
   $ git reset HEAD -- <file>
   ```

2. **查看最后一次提交**
    为 `log -1 HEAD` 添加别名 `last`：

   ```bash
   $ git config --global alias.last 'log -1 HEAD'
   ```

   通过别名快速查看最近一次提交：

   ```bash
   $ git last
   ```

------

#### **执行外部命令**

别名不仅适用于 Git 子命令，还能运行外部命令。为此，可以在命令前加上 `!` 符号。例如：

- 将 `git visual` 定义为运行 Git 图形界面工具 `gitk`：

  ```bash
  $ git config --global alias.visual '!gitk'
  ```

  现在运行以下命令即可打开 `gitk`：

  ```bash
  $ git visual
  ```

------

#### **管理别名**

所有设置的别名会存储在 `~/.gitconfig` 文件中，可以通过编辑该文件管理或调整别名设置：

```bash
[alias]
    co = checkout
    br = branch
    ci = commit
    st = status
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = !gitk
```

