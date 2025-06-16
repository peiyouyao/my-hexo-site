---
title: Git Ignore Rules
date: 2022-12-31 00:00:00
tags: git
categories: tech
---



## 📁 常见忽略规则

### 1. 忽略某些后缀的文件

```gitignore
*.log          # 忽略所有 .log 文件
*.tmp          # 忽略所有 .tmp 文件
*.class        # 忽略所有 .class 文件（Java 编译产物）
```

### 2. 忽略某些特定文件

```gitignore
.idea/         # 忽略 IntelliJ IDEA 配置目录
*.DS_Store     # 忽略 macOS 的文件浏览器生成的隐藏文件
Thumbs.db      # 忽略 Windows 生成的缩略图缓存文件
.env           # 忽略环境变量配置文件
```

### 3. 忽略文件夹及其所有内容

```gitignore
/tmp/          # 忽略 tmp 文件夹及其所有内容
/.idea/        # 忽略项目根目录下的 .idea 文件夹
node_modules/  # 忽略 JavaScript 项目的依赖文件夹
build/         # 忽略构建目录
dist/          # 忽略打包输出目录
```

### 4. 忽略某一模式下的文件

```gitignore
logs/**/*.log      # 忽略 logs 目录及其子目录下所有 .log 文件
**/target/         # 忽略所有层级中的 target 文件夹（例如 Maven 项目）
```

### 5. 例外规则（排除某些文件）

```gitignore
*.secret           # 忽略所有 .secret 文件
!important.secret  # 但不忽略 important.secret 文件
```

---

### 📌 1. `dirname/`

✅ **最常用**的写法，用于**忽略一个目录及其所有内容**，无论该目录在哪个子路径下出现：

```
node_modules/
```

- 会忽略所有路径下的 `node_modules/` 文件夹及其内容。
- 相当于 `**/node_modules/`。
- 建议：**用于忽略文件夹时推荐加上 `/`**，更明确。

------

### 📌 2. `/dirname/`

用于**只忽略项目根目录下的 `dirname/` 文件夹及其内容**：

```
/.idea/
```

- 只会忽略项目根目录下的 `.idea/`。
- 不会忽略 `subdir/.idea/`。
- 建议：当你**只想忽略某个固定路径**的目录时使用。

------

### 📌 3. `dirname`

这个写法会匹配**文件或文件夹名为 `dirname` 的任何路径**：

```
.idea
```

- 会忽略所有名为 `.idea` 的文件或文件夹，不论是否带 `/`。
- 也就是说：`.idea` 文件、`.idea/` 目录都忽略。
- 不如明确写成 `.idea/` 可读性高。

------

### 📌 4. `/dirname`

这个写法会匹配**项目根目录下名为 `dirname` 的文件或文件夹**：

```
/.env
```

- 只忽略根目录下的 `.env` 文件或目录。
- 不会忽略 `config/.env`。

------

### ✅ 总结推荐表

| 写法        | 匹配路径                                | 推荐用途                     |
| ----------- | --------------------------------------- | ---------------------------- |
| `dirname/`  | 所有路径下的 `dirname` 文件夹及内容     | 忽略所有同名文件夹（最常用） |
| `/dirname/` | 仅根目录下的 `dirname` 文件夹及内容     | 只忽略根目录特定文件夹       |
| `dirname`   | 所有路径下的 `dirname` 文件或文件夹     | 通配更广，可能过于宽泛       |
| `/dirname`  | 仅根目录下名为 `dirname` 的文件或文件夹 |                              |

## 通用`.gitignore`写法​

```
# ===== Node.js / Frontend =====
node_modules/
dist/
build/
.npm/
.cache/
.vite/
coverage/
*.log

# ===== React Native / Expo (可选) =====
.expo/
.expo-shared/

# ===== Vue / Vite / Webpack =====
*.local
*.temp
*.bak
*.old

# ===== Python / Flask / Django =====
__pycache__/
*.py[cod]
*.pyo
env/
venv/
*.egg-info/

# ===== Java / Maven / Gradle =====
*.class
*.jar
*.war
*.ear
*.iml
target/
build/
out/
.gradle/
.idea/
*.log

# ===== Go =====
bin/
vendor/
*.test
*.exe
*.out
*.mod

# ===== Rust (如有) =====
/target/

# ===== IntelliJ / VSCode / IDE =====
*.iws
*.ipr
*.iml
.idea/
.vscode/
*.swp
*.swo
.DS_Store

# ===== Logs / System =====
*.log
*.pid
*.seed
*.pid.lock
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*

# ===== Docker / Containers =====
docker-compose.override.yml
.env
.env.*
.env.local
.dockerignore

# ===== Others (general binary/filesystem trash) =====
*.bak
*.tmp
*.temp
*.old
*.orig
Thumbs.db
Desktop.ini
$RECYCLE.BIN/

# ===== Test / Coverage / Reports =====
coverage/
test-results/
junit-report/
lcov-report/
/dump.rdb
```

