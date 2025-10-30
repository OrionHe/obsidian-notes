这三个命令的区别是 Git 中最容易混淆但又非常重要的内容之一。  
我们先来看一个总体概念，然后用具体例子说明：

---

## 🌳 Git 中的三个重要“区域”

1. **HEAD**：当前分支的最近一次提交（commit）
    
2. **暂存区（Index / Staging area）**：`git add` 的结果
    
3. **工作区（Working directory）**：你实际编辑的文件
    

```
HEAD  ←  Index  ←  Working Directory
```

---

## 🧩 三种 reset 模式的区别

|命令|HEAD 指向|暂存区变化|工作区变化|常见用途|
|---|---|---|---|---|
|`git reset --soft <commit>`|✅ 改变|❌ 保留|❌ 保留|回退提交但保留更改（重新提交）|
|`git reset --mixed <commit>` _(默认)_|✅ 改变|✅ 清空|❌ 保留|撤销提交并取消暂存（重新 add）|
|`git reset --hard <commit>`|✅ 改变|✅ 清空|✅ 清空|完全回退（所有更改丢失）|

---

## 🧠 举个例子理解

假设你的提交历史是：

```
A -- B -- C (HEAD)
```

你执行：

```bash
git reset --soft B
```

### `--soft`

- HEAD 从 C 回到 B
    
- **暂存区、工作区都保留**  
    👉 效果：就像撤销了提交 C，但改动还在（可以重新提交）
    

---

### `--mixed`（默认）

```bash
git reset --mixed B
```

- HEAD 从 C 回到 B
    
- **暂存区清空**（C 的改动不再被 add）
    
- **工作区保留文件更改**  
    👉 效果：代码改动还在，但需要重新 `git add`
    

---

### `--hard`

```bash
git reset --hard B
```

- HEAD 从 C 回到 B
    
- 暂存区、工作区都回到 B 的状态  
    👉 效果：所有改动 **彻底消失**
    

---

## 🧰 实际使用场景

|场景|建议命令|说明|
|---|---|---|
|提交了但想重新合并多次提交|`git reset --soft HEAD~2`|保留改动，重新 commit|
|提交后发现文件没 add 全|`git reset --mixed HEAD~1`|撤销提交并重新添加文件|
|想彻底放弃所有未提交的更改|`git reset --hard HEAD`|一键清空|

---

## ⚠️ 注意

- `--hard` **无法恢复**，除非你能从 `git reflog` 找回。
    
- `git reset` 只影响 **本地仓库**。  
    如果改动已 `push` 到远程，建议用 `git revert` 代替。
    

---

一般常用取消commit命令
```
git reset --soft HEAD^
```
