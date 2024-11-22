# Git

### 1. **Git 初始化和配置**

#### 初始化仓库

- `git init`：初始化一个新的 Git 仓库。

  ```shell
  git init
  # 配置ssh密钥
  ssh-keygen -t rsa -C "xxxxxxxxx@xxx.com"
  # 将ssh生成的.pub文件中公钥添加到github中后即可推送代码
  git push origin master
  ```

#### 配置用户信息

- **`git config --global user.name "Your Name"`**：设置全局用户名。

- `git config --global user.email "you@example.com"`：设置全局用户邮箱。

  ```shell
  git config --global user.name "Your Name"
  git config --global user.email "you@example.com"
  ```

#### 查看配置

- **`git config --list`**：查看 Git 的配置列表。

### 2. **基础操作**

#### 克隆仓库

- `git clone <repository-url>`：从远程仓库克隆项目到本地。

  ```shell
  git clone https://github.com/user/repo.git
  ```

#### 查看状态

- `git status`：查看当前工作目录状态，包括修改、未跟踪文件等。

  ```shell
  git status
  ```

#### 查看日志

- `git log`：查看提交历史。

  ```shell
  git log
  ```

#### 查看分支

- `git branch`：查看本地所有分支。

  ```shell
  git branch
  ```

### 3. **文件管理**

#### 添加文件

- **`git add <file>`**：将单个文件添加到暂存区。

- `git add .`：将当前目录下的所有文件添加到暂存区。

  ```shell
  git add file.txt
  git add .
  ```

#### 提交更改

- `git commit -m "Commit message"`：提交暂存区的文件到本地仓库。

  ```shell
  git commit -m "Initial commit"
  ```

#### 修改最后一次提交

- `git commit --amend`：修改上一次提交的信息（用于修正提交的错误）。

  ```shell
  git commit --amend -m "Updated commit message"
  ```

#### 删除文件

- `git rm <file>`：删除文件，并提交更改。

  ```shell
  git rm file.txt
  ```

### 4. **分支操作**

#### 合并分支

* `git branch -m main master`：将当前分支名称main修改为master
* `git pull --rebase origin master`：获取远程master更新，将更新应用到本地分支上，本地的提交放在这些更新之后例如远程三次提交A->B->C；本地两次提交D->E；合并后变为A->B->C->D->E

#### 创建分支

- `git branch <branch-name>`：创建一个新的分支。

  ```shell
  git branch feature-branch
  ```

#### 切换分支

- `git checkout <branch-name>`：切换到指定分支。

  ```shell
  git checkout feature-branch
  ```

#### 远程分支

* `git checkout -b feature-branch origin/feature-branch`：切换到远程分支，并且创建这个远程分支对应的本地分支
* `git switch -c my-local-branch`：如果使用`git checkout origin/main`时使用的是远程分支`main`，并不会对本地修改造成影响。在分离的 HEAD 状态下所做的提交不会被本地保存，你可以创建一个新的本地分支来保存这些更改。你可以使用 `git switch -c <new-branch-name>` 命令来创建一个新的分支并切换到它

#### 创建并切换到新分支

- `git checkout -b <branch-name>`：创建并切换到新的分支。

  ```shell
  git checkout -b feature-branch
  ```

#### 合并分支

- `git merge <branch-name>`：将指定分支合并到当前分支。

  ```shell
  git merge feature-branch
  ```

#### 删除分支

- `git branch -d <branch-name>`：删除本地分支。

  ```shell
  git branch -d feature-branch
  ```

### 5. **远程仓库操作**

#### 查看远程仓库

- `git remote -v`：查看远程仓库的地址。

  ```shell
  git remote -v
  ```

#### 添加远程仓库

- `git remote add <name> <url>`：为本地仓库添加远程仓库。

  ```shell
  git remote add origin https://github.com/user/repo.git
  ```
  
- `git remote set-url <New-url>`：修改本地远程仓库的地址

#### 推送代码

- `git push <remote> <branch>`：将本地分支推送到远程仓库。

  ```shell
  git push origin master
  ```

#### 拉取代码

- `git pull`：从远程仓库拉取最新的代码并合并到当前分支。

  ```shell
  git pull origin master
  ```

#### 克隆仓库

- `git clone <repository-url>`：从远程仓库克隆项目到本地。

  ```shell
  git clone https://github.com/user/repo.git
  ```

### 6. **撤销更改**

#### 撤销工作目录中的修改

- `git checkout -- <file>`：撤销某个文件的修改，将其恢复到上一次提交的状态。

  ```shell
  git checkout -- file.txt
  ```
  
- `git restore <file>`：撤销工作区的修改，将文件回到最近一次提交的状态

- `git restore --staged <file>`：撤销暂存区中某个文件的修改，不会影响到工作区

#### 撤销暂存区的更改

- `git reset <file>`：将文件从暂存区移除，但保留工作目录中的修改。

  ```
  git reset file.txt
  ```

#### 撤销提交

- **`git reset --soft <commit>`**：将代码回退到某个提交，并保留工作目录中的更改。

- `git reset --hard <commit>`：将代码回退到某个提交，并删除所有更改。

  ```shell
  bash复制代码git reset --soft HEAD~1  // 撤销上一次提交，但保留文件改动
  git reset --hard HEAD~1  // 完全撤销上一次提交和更改
  ```

### 7. **Git 分支操作**

#### 显示分支

- **`git branch`**：查看所有分支。
- **`git branch -a`**：查看本地和远程的所有分支。

#### 合并分支

- `git merge <branch-name>`：合并指定分支到当前分支。

  ```shell
  git merge feature-branch
  ```

#### 删除分支

- `git branch -d <branch-name>`：删除本地分支。

  ```shell
  git branch -d feature-branch
  ```

### 8. **标签操作**

#### 创建标签

- `git tag <tag-name>`：为当前的提交创建一个标签。

  ```shell
  git tag v1.0
  ```

#### 查看标签

- `git tag`：列出所有标签。

  ```shell
  git tag
  ```

#### 推送标签到远程仓库

- `git push origin <tag-name>`：将标签推送到远程仓库。

  ```shell
  git push origin v1.0
  ```

### 9. **Git 协作**

#### Fetch 获取最新的远程更新

- `git fetch <remote>`：从远程获取最新的代码更新，但不合并到当前分支。

  ```shell
  git fetch origin
  ```

#### Rebase 变基操作

- `git rebase <branch-name>`：将当前分支的更改应用到另一个分支上，使历史记录更加线性。

  ```shell
  git rebase master
  ```

### 10. **查看差异**

#### 查看文件差异

- `git diff`：查看工作目录中未提交的文件差异。

  ```shell
  git diff
  ```

#### 查看提交间的差异

- `git diff <commit1> <commit2>`：查看两个提交之间的差异。

  ```shell
  git diff commit1 commit2
  ```

### 11. **其他命令**

#### Stash 临时保存更改

- `git stash`：保存当前工作目录中的修改，而不提交。

  ```shell
  git stash
  ```

#### 恢复 `stash`

- `git stash apply`：恢复最近一次存储的修改。

  ```shell
  git stash apply
  ```

#### 清理未跟踪的文件

- `git clean -f`：清理未跟踪的文件。

  ```shell
  git clean -f
  ```