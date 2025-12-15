```bat
@echo off
setlocal EnableExtensions EnableDelayedExpansion

:: 让中文提示正常显示（UTF-8）；脚本结束时会恢复原代码页
for /f "tokens=2 delims=:" %%A in ('chcp') do set "origChcp=%%A"
set "origChcp=!origChcp: =!"
chcp 65001 >nul

pushd "%~dp0" >nul 2>&1

:: 更改文本颜色为亮绿色
color 0A
title 配置无密码 SSH 连接

:: 依赖检查（Windows OpenSSH）
where ssh >nul 2>&1 || (
  echo [ERROR] 未找到 ssh。请在“可选功能”里安装 OpenSSH Client。
  goto :end
)
where ssh-keygen >nul 2>&1 || (
  echo [ERROR] 未找到 ssh-keygen。请在“可选功能”里安装 OpenSSH Client。
  goto :end
)

:: 默认连接参数（可直接回车使用默认值）
set "host=172.17.0.100"
set "port=4036"
set "username="

echo.
set /p hostInput=请输入主机IP/域名 [默认: %host%] :
if defined hostInput set "host=!hostInput!"

set /p portInput=请输入端口 [默认: %port%] :
if defined portInput set "port=!portInput!"

:askUser
set /p username=请输入用户名 (必填) :
if not defined username (
  echo 用户名不能为空，请重新输入。
  goto :askUser
)

:: 密钥位置（使用用户目录下的 .ssh）
set "sshDir=%USERPROFILE%\.ssh"
:: 兼容性优先：默认使用 RSA 2048（如需更高安全/更短密钥，可改为 ed25519）
set "keyType=rsa"
set "keyBits=2048"
set "keyFile=%sshDir%\id_!keyType!"
set "pubFile=%keyFile%.pub"

if not exist "%sshDir%" mkdir "%sshDir%" >nul 2>&1

echo.
echo 步骤1: 准备 SSH 密钥对（%keyType%）
if exist "%keyFile%" (
  if exist "%pubFile%" (
    echo 已存在密钥: "%keyFile%"
  ) else (
    echo [WARN] 找到私钥但缺少公钥，将尝试重新生成公钥...
    ssh-keygen -y -f "%keyFile%" > "%pubFile%"
    if errorlevel 1 (
      echo [ERROR] 无法从私钥生成公钥，请检查 "%keyFile%"
      goto :end
    )
  )
) else (
  echo 正在生成密钥（不设置口令，后续登录不再提示密码/口令）...
  if /i "%keyType%"=="rsa" (
    ssh-keygen -t rsa -b %keyBits% -a 64 -f "%keyFile%" -N "" -C "%username%@%COMPUTERNAME%"
  ) else (
    ssh-keygen -t %keyType% -a 64 -f "%keyFile%" -N "" -C "%username%@%COMPUTERNAME%"
  )
  if errorlevel 1 (
    echo [ERROR] ssh-keygen 失败。
    goto :end
  )
)

echo.
echo 步骤2: 将公钥写入远端 authorized_keys（会提示输入一次远端登录密码）

:: 优先使用 WSL 的 ssh-copy-id（更稳妥，会去重/修权限）；没有 WSL 时使用纯 Windows 方式追加
where wsl >nul 2>&1
if errorlevel 1 (
  goto :windowsCopy
) else (
  call :toWslPath "%pubFile%" pubFileWsl
  if not defined pubFileWsl (
    echo [WARN] 无法获取公钥的 WSL 路径，将尝试使用 Windows 方式追加公钥...
    goto :windowsCopy
  )
  wsl -e ssh-copy-id -i "!pubFileWsl!" -p "!port!" "!username!@!host!"
  if errorlevel 1 (
    echo [WARN] ssh-copy-id 失败，将尝试使用 Windows 方式追加公钥...
    goto :windowsCopy
  )
)
goto :afterCopy

:windowsCopy
:: 将公钥通过 ssh 追加到远端 ~/.ssh/authorized_keys（不依赖 WSL；会提示输入一次远端登录密码）
type "%pubFile%" | ssh -p "!port!" "!username!@!host!" "mkdir -p ~/.ssh && chmod 700 ~/.ssh && (command -v tr >/dev/null 2>&1 && tr -d '\r' || cat) >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
if errorlevel 1 (
  echo [ERROR] 追加公钥失败（可能远端 shell 不兼容，或缺少 tr 等工具）。
  echo 你可以手动将 "%pubFile%" 内容追加到远端: ~/.ssh/authorized_keys
  goto :end
)

:afterCopy
echo.
echo 步骤3: 验证无密码连接（不应再提示输入远端密码）
ssh -o StrictHostKeyChecking=accept-new -o BatchMode=yes -o ConnectTimeout=8 -p "!port!" "!username!@!host!" "echo SSH_OK"
if errorlevel 1 (
  echo [WARN] 仍然需要密码/或连接失败。请检查：
  echo   1^) 远端是否启用公钥认证（sshd_config: PubkeyAuthentication yes）
  echo   2^) 远端 ~/.ssh 和 authorized_keys 权限是否正确（700/600）
  echo   3^) 端口/用户名/主机是否正确
  echo   4^) 是否被 Fail2ban/防火墙拦截
  echo.
  echo 可尝试用更详细日志排查：
  echo   ssh -vvv -p "!port!" "!username!@!host!"
) else (
  echo OK: 已可无密码登录。
)

goto :end

:: 将 Windows 路径转换为 WSL 路径（输出到变量名）
:: 用法: call :toWslPath "C:\path\file" outVar
:toWslPath
set "inPath=%~1"
for /f "usebackq delims=" %%A in (`wsl -e wslpath -a "%inPath%"`) do set "%~2=%%A"
exit /b 0

:end
if defined origChcp chcp !origChcp! >nul
popd >nul 2>&1
endlocal
pause

```