# Git Submodule 使用指南

Git 子模块允许你将一个 Git 仓库作为另一个 Git 仓库的子目录。这对于管理依赖关系或将多个项目组合在一起非常有用。

## 基本用法

### 添加子模块
git submodule add <repository> [path]

### 克隆带子模块的项目
# 方法1：
git clone <repository>
git submodule init
git submodule update

# 方法2：
git clone --recurse-submodules <repository>

### 更新子模块
git submodule update --remote
git submodule update --remote [submodule_name]

### 删除子模块
git submodule deinit -f [path]
rm -rf .git/modules/[path]
git rm -f [path]

## 常用命令

### 状态查看
git submodule status
git submodule summary

### 批量操作
git submodule foreach 'git pull origin master'

## 最佳实践
1. 固定子模块版本到特定提交
2. 先提交子模块再提交主项目
3. 及时更新文档
4. 定期检查更新

## 常见问题处理
1. 子模块游离HEAD：
   git checkout master
   git pull origin master

2. 更新失败：
   git submodule update --init --recursive --force

3. 路径变更：
   git submodule sync

## 注意事项
- 子模块更改需要分别提交
- 克隆时注意初始化
- 注意版本兼容性
- 完整清理配置

## git submodule update --init 说明

这个命令实际上是组合了两个操作：
1. `git submodule init`: 初始化本地配置文件
2. `git submodule update`: 从远程获取子模块代码

### 具体工作流程

1. `git submodule init`
- 初始化 .git/config 文件
- 将 .gitmodules 中的子模块信息注册到 .git/config 中
- 不会下载子模块代码

2. `git submodule update`
- 根据主项目记录的子模块 commit id
- 拉取对应的子模块代码
- 将子模块置于分离头指针(detached HEAD)状态

### 使用场景

1. 首次克隆含子模块的项目后：

