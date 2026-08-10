# Git 常用命令使用大全

> 定位：日常开发、团队协作与误操作恢复的进阶速查手册。  
> 建议：执行回滚命令前先运行 `git status`、`git log --oneline --decorate -n 10`，重要改动先创建临时分支备份。

---

## 目录

- [1. 核心概念与命令格式](#1-核心概念与命令格式)
- [2. 安装检查与基础配置](#2-安装检查与基础配置)
- [3. 创建与获取仓库](#3-创建与获取仓库)
- [4. 查看仓库状态和差异](#4-查看仓库状态和差异)
- [5. 暂存与提交](#5-暂存与提交)
- [6. 分支操作](#6-分支操作)
- [7. 合并、变基与拣选提交](#7-合并变基与拣选提交)
- [8. 远程仓库协作](#8-远程仓库协作)
- [9. 临时保存工作](#9-临时保存工作)
- [10. 标签管理](#10-标签管理)
- [11. 日志、搜索与定位问题](#11-日志搜索与定位问题)
- [12. 回滚与恢复总览](#12-回滚与恢复总览)
- [13. 回滚：本地未提交修改](#13-回滚本地未提交修改)
- [14. 回滚：本地提交](#14-回滚本地提交)
- [15. 回滚：已推送提交](#15-回滚已推送提交)
- [16. 恢复误删分支或丢失提交](#16-恢复误删分支或丢失提交)
- [17. 冲突处理](#17-冲突处理)
- [18. 清理、忽略与仓库维护](#18-清理忽略与仓库维护)
- [19. 常用工作流示例](#19-常用工作流示例)
- [20. 实用别名与速查表](#20-实用别名与速查表)

---

## 1. 核心概念与命令格式

### 1.1 Git 的四个区域

```text
工作区 Working Tree
    ↓ git add
暂存区 Staging Area / Index
    ↓ git commit
本地仓库 Local Repository
    ↓ git push
远程仓库 Remote Repository
```

### 1.2 常用引用

| 引用 | 含义 |
|---|---|
| `HEAD` | 当前检出的提交 |
| `HEAD~1` 或 `HEAD^` | 当前提交的第 1 个父提交 |
| `HEAD~3` | 当前提交向前数 3 个父提交 |
| `main` | 名为 main 的本地分支 |
| `origin/main` | 远程跟踪分支 |
| `a1b2c3d` | 提交哈希的唯一短前缀 |

### 1.3 查看命令帮助

```bash
git help <command>
git <command> --help
git <command> -h
```

示例：

```bash
git commit -h
git help reset
```

---

## 2. 安装检查与基础配置

### 2.1 检查版本

```bash
git --version
```

### 2.2 配置用户信息

```bash
# 全局配置：对当前用户的所有仓库生效
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# 仅对当前仓库生效
git config user.name "Your Name"
git config user.email "you@example.com"
```

### 2.3 查看配置

```bash
git config --list
git config --global --list
git config --local --list
git config --show-origin --list
```

### 2.4 常用推荐配置

```bash
# 新仓库默认分支名
git config --global init.defaultBranch main

# 拉取时默认使用变基，减少无意义的合并提交
git config --global pull.rebase true

# 自动删除已被远程移除的跟踪引用
git config --global fetch.prune true

# 提交信息编辑器示例：VS Code
git config --global core.editor "code --wait"
```

> `pull.rebase true` 会改变 `git pull` 的整合方式。团队已有明确规范时，以团队规范为准。

---

## 3. 创建与获取仓库

### 3.1 初始化仓库

```bash
# 在当前目录初始化
git init

# 创建目录并初始化
git init my-project

# 指定初始分支
git init -b main
```

### 3.2 克隆仓库

```bash
git clone <repository-url>
git clone <repository-url> <directory>

# 只克隆最近一层历史，适合大型仓库快速下载
git clone --depth 1 <repository-url>

# 克隆指定分支
git clone -b <branch> <repository-url>
```

### 3.3 浅克隆补全历史

```bash
git fetch --unshallow
```

---

## 4. 查看仓库状态和差异

### 4.1 查看状态

```bash
git status
git status -s       # 简洁输出
git status -sb      # 简洁输出，并显示分支跟踪关系
```

简洁状态中的常见标记：

| 标记 | 含义 |
|---|---|
| `M` | 已修改 |
| `A` | 新增 |
| `D` | 删除 |
| `R` | 重命名 |
| `??` | 未跟踪 |

### 4.2 查看差异

```bash
# 工作区与暂存区的差异
git diff

# 暂存区与最近一次提交的差异
git diff --staged
# 等价写法
git diff --cached

# 两个提交之间的差异
git diff <commit-a> <commit-b>

# 某个文件的差异
git diff -- path/to/file

# 只看发生变化的文件名
git diff --name-only

# 查看变更统计
git diff --stat
```

### 4.3 查看文件内容

```bash
# 查看某次提交中的文件
git show <commit>:path/to/file

# 查看当前分支最近提交的完整信息
git show HEAD
```

---

## 5. 暂存与提交

### 5.1 添加到暂存区

```bash
git add <file>
git add <directory>
git add .             # 添加当前目录下全部变更
git add -A            # 添加整个仓库全部变更
git add -u            # 只添加已跟踪文件的修改和删除
git add -p            # 交互式按代码块选择暂存
```

> 提交前推荐执行 `git diff --staged`，确认实际将要提交的内容。

### 5.2 创建提交

```bash
git commit -m "feat: add login page"

# 打开编辑器编写详细提交信息
git commit

# 自动暂存已跟踪文件并提交；不包含未跟踪文件
git commit -am "fix: correct validation"
```

### 5.3 修改最近一次提交

```bash
# 修改最近一次提交信息
git commit --amend -m "new commit message"

# 将遗漏的文件补入最近一次提交，并保留原提交信息
git add <forgotten-file>
git commit --amend --no-edit
```

> `--amend` 会生成新的提交哈希。最近提交若已推送到共享分支，优先新增修复提交，不要随意 amend。

### 5.4 删除或移动文件

```bash
git rm <file>
git rm -r <directory>

# 只停止跟踪，保留本地文件
git rm --cached <file>

git mv <old-path> <new-path>
```

---

## 6. 分支操作

### 6.1 查看分支

```bash
git branch               # 本地分支
git branch -r            # 远程跟踪分支
git branch -a            # 全部分支
git branch -vv           # 显示上游关系和最近提交
git branch --merged      # 已合并到当前分支的分支
git branch --no-merged   # 尚未合并的分支
```

### 6.2 创建和切换分支

推荐使用 Git 2.23+ 的 `switch`：

```bash
git switch <branch>
git switch -c <new-branch>
git switch -c <new-branch> <start-point>
git switch -             # 切回上一个分支
```

传统写法：

```bash
git checkout <branch>
git checkout -b <new-branch>
```

### 6.3 重命名分支

```bash
# 重命名当前分支
git branch -m <new-name>

# 重命名指定分支
git branch -m <old-name> <new-name>
```

### 6.4 删除分支

```bash
# 安全删除：分支未合并时会拒绝
git branch -d <branch>

# 强制删除：可能丢失未合并提交
git branch -D <branch>

# 删除远程分支
git push origin --delete <branch>
```

> 删除前可运行 `git branch --no-merged`。不确定时先创建备份标签：`git tag backup/<name> <branch>`。

### 6.5 设置上游分支

```bash
# 首次推送并建立跟踪关系
git push -u origin <branch>

# 当前分支跟踪指定远程分支
git branch --set-upstream-to=origin/<branch>

# 取消上游关系
git branch --unset-upstream
```

---

## 7. 合并、变基与拣选提交

### 7.1 合并分支

```bash
git switch main
git merge <feature-branch>

# 始终创建合并提交
git merge --no-ff <feature-branch>

# 只允许快进合并，否则失败
git merge --ff-only <feature-branch>

# 中止发生冲突的合并
git merge --abort
```

### 7.2 变基

```bash
git switch feature
git rebase main

# 交互式整理最近 3 个提交
git rebase -i HEAD~3

# 解决冲突后继续
git add <resolved-files>
git rebase --continue

# 跳过当前提交
git rebase --skip

# 放弃本次变基
git rebase --abort
```

> 重要原则：不要对其他人已经基于其开发的共享提交执行 rebase。变基会改写提交历史。

### 7.3 拣选提交

```bash
# 将指定提交应用到当前分支
git cherry-pick <commit>

# 只应用改动，不立即提交
git cherry-pick -n <commit>

# 连续范围：包含 A 的后一个提交至 B
git cherry-pick A..B

# 解决冲突后继续
git cherry-pick --continue

# 放弃本次操作
git cherry-pick --abort
```

---

## 8. 远程仓库协作

### 8.1 查看和管理远程仓库

```bash
git remote -v
git remote show origin

git remote add origin <repository-url>
git remote set-url origin <new-url>
git remote rename origin upstream
git remote remove <remote>
```

常见命名：

- `origin`：自己克隆或主要推送的远程仓库。
- `upstream`：Fork 工作流中的上游原仓库。

### 8.2 获取远程更新

```bash
# 下载远程数据，不自动整合到当前分支
git fetch origin

# 获取所有远程仓库
git fetch --all

# 获取并清理已失效的远程跟踪分支
git fetch --prune
```

### 8.3 拉取更新

```bash
# 等价于 fetch + merge（取决于配置）
git pull

# 明确使用变基整合
git pull --rebase

# 只允许快进，避免自动产生合并提交
git pull --ff-only
```

### 8.4 推送更新

```bash
git push
git push origin <branch>
git push -u origin <branch>

git push --tags                 # 推送所有标签
git push origin <tag>           # 推送指定标签
```

### 8.5 安全强制推送

```bash
git push --force-with-lease origin <branch>
```

> `--force-with-lease` 会在远程分支已被他人更新时拒绝覆盖，比 `--force` 安全。共享分支仍应尽量避免强推。

### 8.6 同步 Fork 仓库

```bash
git remote add upstream <upstream-url>
git fetch upstream
git switch main
git merge --ff-only upstream/main
git push origin main
```

---

## 9. 临时保存工作

### 9.1 保存与恢复

```bash
# 保存已跟踪文件的修改
git stash push -m "WIP: login page"

# 同时保存未跟踪文件
git stash push -u -m "WIP: include untracked files"

# 交互式保存部分修改
git stash push -p

git stash list
git stash show -p stash@{0}

# 应用但不删除 stash
git stash apply stash@{0}

# 应用并删除 stash
git stash pop stash@{0}
```

### 9.2 删除 stash

```bash
git stash drop stash@{0}
git stash clear
```

> `git stash clear` 会清空所有 stash，恢复较困难。执行前先运行 `git stash list`。

### 9.3 从 stash 创建分支

```bash
git stash branch <new-branch> stash@{0}
```

适合 stash 与当前代码差异较大、直接恢复容易冲突的情况。

---

## 10. 标签管理

### 10.1 创建标签

```bash
# 轻量标签
git tag v1.0.0

# 附注标签，推荐用于正式版本
git tag -a v1.0.0 -m "Release v1.0.0"

# 给指定提交打标签
git tag -a v1.0.0 <commit> -m "Release v1.0.0"
```

### 10.2 查看标签

```bash
git tag
git tag -l "v1.*"
git show v1.0.0
```

### 10.3 推送和删除标签

```bash
git push origin v1.0.0
git push origin --tags

# 删除本地标签
git tag -d v1.0.0

# 删除远程标签
git push origin --delete v1.0.0
```

---

## 11. 日志、搜索与定位问题

### 11.1 查看提交历史

```bash
git log
git log --oneline
git log --oneline --graph --decorate --all
git log -n 10
git log --since="2 weeks ago"
git log --author="Alice"
git log -- path/to/file
```

### 11.2 查看每次提交的改动

```bash
git log -p
git log --stat
git show <commit>
git show --name-only <commit>
```

### 11.3 搜索提交和代码变化

```bash
# 按提交信息搜索
git log --grep="fix login"

# 查找新增或删除了指定字符串的提交
git log -S "functionName" --oneline

# 使用正则查找差异中匹配的提交
git log -G "pattern" --oneline
```

### 11.4 查看单行责任归属

```bash
git blame path/to/file
git blame -L 20,40 path/to/file
```

### 11.5 二分定位缺陷提交

```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>

# Git 检出中间提交后，测试并反复标记
git bisect good
# 或
git bisect bad

# 完成后退出
git bisect reset
```

### 11.6 查看引用变更记录

```bash
git reflog
git reflog show <branch>
```

`reflog` 是恢复误 reset、误 rebase、误删分支的重要工具。它通常只保存在本地，不会随 push 发送到远程。

---

## 12. 回滚与恢复总览

### 12.1 先判断改动处于哪个阶段

| 当前情况 | 首选命令 | 是否改写历史 |
|---|---|---|
| 工作区文件已修改，未 `git add` | `git restore <file>` | 否 |
| 已 `git add`，尚未提交 | `git restore --staged <file>` | 否 |
| 新增了未跟踪文件 | 手动处理；先用 `git clean -n` 预览 | 否 |
| 最近提交信息或内容有误，未推送 | `git commit --amend` | 是 |
| 本地提交需回退，未推送 | `git reset` | 是 |
| 提交已推送到共享分支 | `git revert` | 否，新增反向提交 |
| 误 reset/rebase/删分支 | `git reflog` 后创建恢复分支 | 否 |

### 12.2 三种 reset 模式

假设当前历史为 `A - B - C`，执行 `git reset <target>`：

| 模式 | 分支指针 | 暂存区 | 工作区 | 典型用途 |
|---|---|---|---|---|
| `--soft` | 移动 | 保留 | 保留 | 撤销提交，重新组织提交 |
| `--mixed` | 移动 | 重置 | 保留 | 撤销提交和暂存，保留代码；默认模式 |
| `--hard` | 移动 | 重置 | 重置 | 完全丢弃目标提交后的本地改动 |

> **高风险：** `git reset --hard` 会丢弃工作区和暂存区修改。执行前先确认 `git status`，必要时创建备份分支：`git branch backup-before-reset`。

### 12.3 revert 与 reset 的区别

- `git revert`：新增一个“反向提交”，不改写已有历史，适合已推送和多人协作分支。
- `git reset`：移动分支指针，可能让提交从当前历史消失，适合尚未推送的本地提交。

---

## 13. 回滚：本地未提交修改

### 13.1 丢弃单个文件在工作区的修改

```bash
git restore path/to/file
```

恢复全部已跟踪文件：

```bash
git restore .
```

传统写法：

```bash
git checkout -- path/to/file
```

> 这些命令会覆盖未提交修改。若可能还需要代码，先运行 `git diff` 并保存补丁，或使用 `git stash push -u`。

### 13.2 撤销 git add，保留文件修改

```bash
git restore --staged path/to/file
git restore --staged .
```

传统写法：

```bash
git reset HEAD path/to/file
```

### 13.3 同时撤销暂存和工作区修改

```bash
git restore --staged path/to/file
git restore path/to/file
```

也可以使用高风险写法：

```bash
git reset --hard HEAD
```

### 13.4 恢复到指定提交中的文件版本

```bash
# 先恢复到工作区，检查后再提交
git restore --source=<commit> path/to/file

# 同时恢复到暂存区和工作区
git restore --source=<commit> --staged --worktree path/to/file
```

### 13.5 清理未跟踪文件

务必先预览：

```bash
git clean -n        # 预览将删除的未跟踪文件
git clean -nd       # 同时预览未跟踪目录
git clean -nx       # 连被 .gitignore 忽略的文件也预览
```

确认后执行：

```bash
git clean -f        # 删除未跟踪文件
git clean -fd       # 删除未跟踪文件和目录
```

> **极高风险：** `git clean` 删除的文件通常不在 Git 历史中，难以恢复。不要直接使用 `git clean -fdx`；先执行对应的 `-n` 预览命令并逐项确认。

---

## 14. 回滚：本地提交

### 14.1 撤销最近一次提交，保留为已暂存

```bash
git reset --soft HEAD~1
```

用途：重新拆分提交、补充文件或修改提交信息。

### 14.2 撤销最近一次提交，保留工作区修改但取消暂存

```bash
git reset HEAD~1
# 等价于
git reset --mixed HEAD~1
```

### 14.3 撤销最近一次提交并丢弃改动

```bash
git reset --hard HEAD~1
```

> 执行前建议：`git branch backup-before-reset`。即使误操作，也可通过该备份分支恢复。

### 14.4 回退多个本地提交

```bash
# 回退最近 3 个提交，保留代码并保持暂存
git reset --soft HEAD~3

# 回退到指定提交，保留代码但取消暂存
git reset --mixed <target-commit>
```

### 14.5 撤销某个旧提交，不改写历史

```bash
git revert <commit>
```

撤销连续范围：

```bash
# 创建多个反向提交
git revert <oldest-commit>^..<newest-commit>

# 先汇总改动，再手动创建一个提交
git revert --no-commit <oldest-commit>^..<newest-commit>
git commit -m "revert: rollback related changes"
```

### 14.6 撤销合并提交

```bash
git revert -m 1 <merge-commit>
```

- `-m 1` 表示保留第 1 个父分支的视角，通常用于在主分支上撤销合并。
- 合并提交的父节点可用 `git show --summary <merge-commit>` 查看。

> 撤销合并前必须确认主线父节点。选错 `-m` 可能保留错误的一侧变更。

---

## 15. 回滚：已推送提交

### 15.1 共享分支的安全做法：revert

```bash
git switch main
git pull --ff-only
git revert <bad-commit>
git push origin main
```

优点：

- 不改写远程历史。
- 其他协作者正常 pull 即可。
- 审计记录清晰。

### 15.2 回滚远程最近多个提交

```bash
git pull --ff-only
git revert --no-commit HEAD~3..HEAD
git commit -m "revert: rollback last three commits"
git push origin <branch>
```

说明：`HEAD~3..HEAD` 包含最近 3 个提交，不包含 `HEAD~3`。

### 15.3 必须改写远程历史时

仅适用于明确允许强推的个人分支或团队已协调的情况：

```bash
# 先创建备份
git branch backup/<branch>-before-rewrite

git reset --hard <target-commit>
git push --force-with-lease origin <branch>
```

不要使用：

```bash
git push --force
```

原因：`--force` 可能静默覆盖他人刚推送的提交；`--force-with-lease` 至少会检查远程引用是否仍符合本地预期。

### 15.4 已推送错误敏感信息

普通 revert 不会从历史中删除敏感信息。应立即：

1. 吊销并轮换已泄露的密钥、令牌或密码。
2. 使用 `git filter-repo` 等历史重写工具清理所有相关提交。
3. 与仓库管理员及协作者协调强制推送和重新克隆。
4. 检查构建缓存、日志、Fork 和制品中是否仍有副本。

---

## 16. 恢复误删分支或丢失提交

### 16.1 使用 reflog 找回提交

```bash
git reflog --date=local
```

找到目标提交哈希后，先创建恢复分支：

```bash
git switch -c recovery/<name> <commit>
```

确认内容正确后再合并或 cherry-pick：

```bash
git switch <target-branch>
git cherry-pick <commit>
```

### 16.2 恢复误删的本地分支

```bash
# 查看分支删除前指向的提交
git reflog --all

# 重新创建分支
git branch <restored-branch> <commit>
```

### 16.3 恢复误 reset 的提交

示例：误执行 `git reset --hard HEAD~3`。

```bash
git reflog
# 找到 reset 之前的 HEAD，例如 abc1234
git switch -c recovery-before-reset abc1234
```

确认无误后，可将原分支恢复到该提交：

```bash
git switch <original-branch>
git reset --hard abc1234
```

### 16.4 恢复误 rebase 前的状态

```bash
git reflog
# 找到 rebase 开始前的提交
git switch -c recovery-before-rebase <commit>
```

部分操作也可参考 Git 自动保留的 `ORIG_HEAD`：

```bash
git show ORIG_HEAD
git branch recovery-orig-head ORIG_HEAD
```

### 16.5 reflog 中也找不到时

```bash
# 查找悬空对象
git fsck --lost-found

# 检查候选提交内容
git show <dangling-commit>

# 创建恢复分支
git branch recovery-fsck <dangling-commit>
```

> Git 的垃圾回收最终可能删除不可达对象。发现误操作后应尽快恢复，避免运行激进的 `git gc --prune=now`。

---

## 17. 冲突处理

### 17.1 查找冲突文件

```bash
git status
git diff --name-only --diff-filter=U
```

冲突标记：

```text
<<<<<<< HEAD
当前分支内容
=======
另一侧内容
>>>>>>> other-branch
```

### 17.2 解决流程

```bash
# 1. 手动编辑冲突文件，删除冲突标记
# 2. 标记为已解决
git add <resolved-file>

# 3A. 合并流程：创建合并提交
git commit

# 3B. 变基流程：继续变基
git rebase --continue

# 3C. cherry-pick 流程：继续拣选
git cherry-pick --continue
```

### 17.3 放弃操作

```bash
git merge --abort
git rebase --abort
git cherry-pick --abort
git revert --abort
```

### 17.4 选择某一侧版本

合并冲突时：

```bash
# 采用当前分支版本
git checkout --ours path/to/file

# 采用被合并分支版本
git checkout --theirs path/to/file

git add path/to/file
```

> 在 rebase 中，`ours` / `theirs` 的语义容易与直觉相反。使用前先通过 `git status` 和 `git show` 确认内容，不要机械套用。

---

## 18. 清理、忽略与仓库维护

### 18.1 `.gitignore` 示例

```gitignore
# 依赖目录
node_modules/

# 构建产物
dist/
build/

# 环境变量
.env
.env.*
!.env.example

# 编辑器和系统文件
.vscode/
.idea/
.DS_Store
```

检查忽略规则来源：

```bash
git check-ignore -v path/to/file
```

已跟踪文件添加到 `.gitignore` 后仍会被跟踪，可执行：

```bash
git rm --cached path/to/file
git commit -m "chore: stop tracking generated file"
```

### 18.2 清理失效远程分支

```bash
git fetch --prune
git remote prune origin --dry-run
git remote prune origin
```

### 18.3 仓库完整性与维护

```bash
git fsck
git count-objects -vH
git gc
```

> 通常 Git 会自动维护仓库，不需要频繁手动执行 `git gc`。

### 18.4 多工作区并行开发

```bash
git worktree list
git worktree add ../project-hotfix hotfix
git worktree remove ../project-hotfix
git worktree prune
```

---

## 19. 常用工作流示例

### 19.1 新功能开发

```bash
git switch main
git pull --ff-only
git switch -c feature/login

# 开发后
git status
git add -p
git diff --staged
git commit -m "feat: add login flow"
git push -u origin feature/login
```

### 19.2 更新功能分支

使用变基保持线性历史：

```bash
git fetch origin
git switch feature/login
git rebase origin/main
# 若该个人分支已经推送过且必须更新远程
git push --force-with-lease
```

或者使用合并保留完整分支历史：

```bash
git fetch origin
git switch feature/login
git merge origin/main
git push
```

### 19.3 紧急修复

```bash
git switch main
git pull --ff-only
git switch -c hotfix/critical-error

# 修复并测试后
git add -A
git commit -m "fix: resolve critical error"
git push -u origin hotfix/critical-error
```

### 19.4 提交到错误分支后的修复

若错误提交尚未推送：

```bash
# 在当前提交上创建正确分支
git switch -c correct-branch

# 回到错误分支，移除该提交
git switch wrong-branch
git reset --hard HEAD~1
```

若错误提交已推送到共享分支：

```bash
# 在正确分支拣选提交
git switch correct-branch
git cherry-pick <commit>

# 在错误分支创建反向提交
git switch wrong-branch
git revert <commit>
```

### 19.5 提交前自检

```bash
git status -sb
git diff
git diff --staged
git log --oneline --decorate -n 5
```

---

## 20. 实用别名与速查表

### 20.1 推荐别名

```bash
git config --global alias.st "status -sb"
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.unstage "restore --staged"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.lg "log --graph --decorate --all --pretty=format:%C(auto)%h%Creset%C(auto)%d%Creset\ %s\ %C(dim white)(%an,%ar)%Creset"
```

使用：

```bash
git st
git lg
git unstage path/to/file
```

### 20.2 高频命令速查

| 目标 | 命令 |
|---|---|
| 查看状态 | `git status -sb` |
| 查看未暂存差异 | `git diff` |
| 查看已暂存差异 | `git diff --staged` |
| 交互式暂存 | `git add -p` |
| 创建提交 | `git commit -m "message"` |
| 新建并切换分支 | `git switch -c <branch>` |
| 切回上一分支 | `git switch -` |
| 获取远程更新 | `git fetch --prune` |
| 拉取并变基 | `git pull --rebase` |
| 首次推送分支 | `git push -u origin <branch>` |
| 暂存当前工作 | `git stash push -u -m "WIP"` |
| 图形化查看历史 | `git log --oneline --graph --decorate --all` |
| 撤销暂存 | `git restore --staged <file>` |
| 丢弃工作区修改 | `git restore <file>` |
| 撤销已推送提交 | `git revert <commit>` |
| 恢复误操作 | `git reflog` |

### 20.3 回滚前的安全检查清单

```text
[ ] 已运行 git status，确认工作区和暂存区状态
[ ] 已运行 git log --oneline --decorate -n 10，确认目标提交
[ ] 已判断提交是否推送、是否为共享分支
[ ] 重要改动已 stash、提交，或创建 backup 分支/标签
[ ] 使用 git clean 前已运行 -n 预览
[ ] 强推时使用 --force-with-lease，而不是 --force
[ ] 优先在恢复分支中验证，再修改原分支
```

---

## 最重要的安全原则

1. **已推送到共享分支：优先 `git revert`。**
2. **未推送的本地提交：按是否保留代码选择 `reset --soft`、`--mixed` 或 `--hard`。**
3. **不确定是否还需要改动：先备份，再回滚。**
4. **误操作后优先查看 `git reflog`，不要立刻执行垃圾回收。**
5. **执行 `reset --hard`、`clean`、强推前，必须再次确认影响范围。**

> 推荐记忆顺序：`status` 看状态，`diff` 看内容，`log` 看历史，`reflog` 找丢失，`revert` 安全反做，`reset` 本地重置。
