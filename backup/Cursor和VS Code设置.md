# 设置文件路径
```TXT
C:\Users\Administrator\AppData\Roaming\Cursor\User
```

# 设置
```json
{
    // 文件关联设置
    "files.associations": {
        "*.h": "c", // 将.h文件识别为C语言文件
        "*.c": "c", // 将.c文件识别为C语言文件
        "*.py": "python", // 将.py文件识别为Python文件
        "*.md": "markdown", // 将.md文件识别为Markdown文件
        "*.sh": "shellscript" // 将.sh文件识别为Shell脚本
    },
    // 编码和保存设置
    "files.encoding": "utf8", // 默认使用UTF-8编码
    "files.autoSave": "afterDelay", // 延迟一段时间后自动保存
    "files.autoSaveDelay": 1000, // 自动保存延迟时间为1000毫秒
    "files.eol": "\n", // 行尾使用LF换行符
    // 特定文件类型的编码设置
    "[c]": {
        "files.encoding": "gb2312" // C语言文件使用GB2312编码
    },
    // 编辑器设置
    "editor.insertSpaces": true, // 插入空格而非Tab字符
    "editor.tabSize": 4, // Tab等于4个空格
    "editor.renderWhitespace": "all", // 显示所有空白字符
    "editor.wordWrap": "on", // 启用自动换行
    "editor.minimap.enabled": false, // 禁用代码缩略图
    // "editor.fontFamily": "'JetBrains Mono', 'Source Code Pro', Consolas, monospace", // 设置编辑器字体
    "editor.fontLigatures": true, // 启用字体连字
    "editor.suggestSelection": "first", // 自动选择第一个建议
    "editor.formatOnSave": false, // 保存时格式化
    "editor.formatOnPaste": false, // 粘贴时格式化
    "editor.mouseWheelZoom": true, // 允许通过Ctrl+滚轮缩放
    // 工作台设置
    "workbench.activityBar.orientation": "vertical", // 活动栏垂直排列
    "workbench.sideBar.location": "right", // 侧边栏位于右侧
    "workbench.colorTheme": "Visual Studio Dark - C++", // 设置颜色主题
    "workbench.iconTheme": "material-icon-theme", // 设置文件图标主题
    "workbench.startupEditor": "none", // 启动时不打开编辑器
    "workbench.editor.enablePreview": false, // 禁用预览模式
    // C/C++设置
    "C_Cpp.errorSquiggles": "disabled", // 禁用C/C++错误波浪线
    // PlantUML设置
    "plantuml.server": "https://www.plantuml.com/plantuml", // PlantUML服务器地址
    // 终端配置
    "terminal.integrated.profiles.windows": {
        "PowerShell": {
            "source": "PowerShell", // PowerShell终端
            "icon": "terminal-powershell" // 使用PowerShell图标
        },
        "Command Prompt": {
            "path": [
                "${env:windir}\\Sysnative\\cmd.exe", // 64位cmd路径
                "${env:windir}\\System32\\cmd.exe" // 32位cmd路径
            ],
            "args": [], // 命令参数
            "icon": "terminal-cmd" // 使用cmd图标
        },
        "Git Bash": {
            "source": "Git Bash" // Git Bash终端
        },
        "Ubuntu-18.04 (WSL)": {
            "path": "C:\\WINDOWS\\System32\\wsl.exe", // WSL路径
            "args": [
                "-d",
                "Ubuntu-18.04" // 指定Ubuntu-18.04发行版
            ]
        }
    },
    "terminal.integrated.defaultProfile.windows": "Ubuntu-18.04 (WSL)", // 默认使用Ubuntu WSL终端
    // 资源管理器设置
    "explorer.confirmDelete": false, // 删除文件时不需确认
    "explorer.confirmDragAndDrop": false, // 拖放文件时不需确认
    // 其他设置
    "terminal.integrated.enableMultiLinePasteWarning": false, // 禁用多行粘贴警告
    "diffEditor.ignoreTrimWhitespace": false, // 比较时不忽略空白字符差异
    "C_Cpp.dimInactiveRegions": false, // 不淡化C/C++非活动区域
    "varTranslation.translationEngine": "baidu", // 变量翻译引擎使用百度
    "git.ignoreLegacyWarning": true, // 忽略Git旧版警告
    // SSH远程平台设置
    "remote.SSH.remotePlatform": {
        "172.17.0.100": "linux", // 指定远程主机为Linux系统
        "192.168.0.68": "linux",
        "192.168.0.96": "linux",
        "172.17.0.159": "linux",
        "100.94.170.40": "linux"
    },
    "cmake.showOptionsMovedNotification": false, // 不显示CMake选项移动通知
    "files.trimTrailingWhitespaceInRegexAndStrings": false, // 不删除正则表达式和字符串中的尾随空格
    // Markdown特定设置
    "[markdown]": {
        "diffEditor.ignoreTrimWhitespace": true // Markdown中比较忽略空白字符
    },
    "C_Cpp.default.compilerPath": "/usr/bin/gcc", // 默认C/C++编译器路径
    // 终端增强
    "terminal.integrated.fontSize": 14, // 终端字体大小
    "terminal.integrated.cursorBlinking": true, // 终端光标闪烁
    "terminal.integrated.cursorStyle": "line", // 终端光标样式为线条
    // Git增强
    "git.enableSmartCommit": true, // 启用智能提交
    "git.confirmSync": false, // 同步时不需确认
    "git.autofetch": true, // 自动拉取远程更新
    // AI相关设置
    "ai.auto.suggest": true, // 启用AI自动建议
    "ai.completions.enabled": true, // 启用AI代码补全
    "ai.completions.proxy.enabled": false, // 禁用AI代理
    // WSL增强
    "remote.WSL.fileWatcher.polling": true, // 使用轮询方式监视WSL文件变化
    "http.proxy": "socks5://127.0.0.1:10808",//应对cursor无法选择claud-4模型
    "http.proxyStrictSSL": false,
    "http.proxySupport": "on"

}
```
