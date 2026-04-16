---
title: Git常用指令
published: 2025-08-09
description: 'Git常用指令'
image: ''
tags: [git,github]
category: 'git'
draft: false 
lang: ''
---

# Git 操作手册（实用全版）

## 一、基础概念

* 工作区：你改代码的地方
* 暂存区（index）：准备提交的内容
* 仓库（repo）：Git 管理的数据
* HEAD：当前指向的提交
* 分支：一条提交链

---

## 二、初始化与配置

```bash
git init                    # 初始化仓库
git clone <url>            # 克隆远程仓库

git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

查看配置：

```bash
git config --list
```

---

## 三、基本流程（最常用）

```bash
git status                 # 查看状态
git add <file>             # 添加到暂存区
git add .                  # 全部添加

git commit -m "说明"       # 提交

git log                    # 查看提交记录
git log --oneline          # 简洁版
```

---

## 四、分支操作

```bash
git branch                 # 查看分支
git branch <name>          # 创建分支
git checkout <name>        # 切换分支
git checkout -b <name>     # 创建并切换

git switch <name>          # 新写法（推荐）

git merge <name>           # 合并分支
```

删除分支：

```bash
git branch -d <name>
```

---

## 五、远程仓库

```bash
git remote -v             # 查看远程
git remote add origin <url>

git push -u origin main   # 推送
git push                  # 后续直接推

git pull                  # 拉取并合并
git fetch                 # 只拉取不合并
```

---

## 六、撤销操作（重点）

### 1. 撤销工作区修改

```bash
git checkout -- <file>
# 或
git restore <file>
```

### 2. 撤销暂存区

```bash
git reset HEAD <file>
```

### 3. 回退提交

```bash
git reset --soft HEAD~1    # 保留代码
git reset --hard HEAD~1    # 全部回退（危险）
```

### 4. 反向提交（安全）

```bash
git revert <commit>
```

---

## 七、查看差异

```bash
git diff                  # 工作区 vs 暂存区
git diff --cached         # 暂存区 vs 仓库
git diff HEAD             # 工作区 vs 最新提交
```

---

## 八、暂存（stash）

```bash
git stash                 # 临时保存
git stash pop             # 恢复并删除
git stash list            # 查看
git stash apply           # 恢复但不删
```

---

## 九、标签（Tag）

```bash
git tag                   # 查看
git tag v1.0              # 创建标签
git push origin v1.0      # 推送标签
```

---

## 十、日志与历史

```bash
git log --graph --oneline --all
git show <commit>
git reflog                # 查看操作历史（救命用）
```

---

## 十一、冲突处理

出现冲突后：

```text
<<<<<<< HEAD
当前分支内容
=======
目标分支内容
>>>>>>> branch
```

处理步骤：

1. 手动修改文件
2. 删除冲突标记
3. `git add`
4. `git commit`

---

## 十二、rebase（进阶）

```bash
git rebase <branch>
```

作用：

* 让提交更线性
* 替代 merge

交互式：

```bash
git rebase -i HEAD~3
```

可：

* 合并提交
* 修改 commit message
* 删除提交

---

## 十三、常见工作流

### 1. 标准开发流程

```bash
git checkout -b feature/xxx
# 开发
git add .
git commit -m "feat: xxx"

git pull --rebase origin main
git push origin feature/xxx
```

---

### 2. 更新本地代码

```bash
git pull --rebase
```

---

## 十四、忽略文件

`.gitignore`

```
node_modules/
dist/
*.log
.env
```

---

## 十五、子模块（了解）

```bash
git submodule add <url>
git submodule update --init --recursive
```

---

## 十六、常见坑

* ❌ `git reset --hard` 会丢数据
* ❌ rebase 后不要强推公共分支
* ❌ 不要在 main 直接开发
* ❌ 忘记 pull 就 push → 冲突

---

## 十七、救命命令

```bash
git reflog                # 找回误删提交
git reset --hard <hash>   # 回到某次状态
```