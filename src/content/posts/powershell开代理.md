---
title: PowerShell 设置代理
published: 2026-05-13
updated: 2026-05-13
description:  PowerShell 设置代理
image: "api"   
tags: [代理]
category: 计算机
draft: false
---



## 方法一：  

为 PowerShell 设置代理，命令如下：

```powershell
$Env:http_proxy="http://127.0.0.1:7890";
$Env:https_proxy="http://127.0.0.1:7890";
```
这是临时命令，重新代理终端需要重新输入  
如果想要永久设置代理，建议使用自定义配置，使每次代理 PowerShell 窗口时，运行如上命令：  

在 PowerShell 窗口中运行如下指令：
```powershell
if (!(Test-Path -Path $PROFILE )) { New-Item -Type File -Path $PROFILE -Force }
notepad $PROFILE
```
默认会使用记事本打开一个文件`C:\Users\{USER_NAME}\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`，在文件中加入上面设置代理的命令，保存关闭即可。

如果下次打开Powershell出现报错`无法加载文件 C:\Users\{USER_NAME}\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1，因为在此系统上禁止运行脚本`，那么需要以管理员身份打开Powershell，然后执行命令`set-executionpolicy remotesigned`（通过`get-ExecutionPolicy`命令可以查看当前的执行策略）。关闭再重新打开即可  
[Powershell 脚本数字签名](https://www.pstips.net/powershell-scripts-signature.html)  

## 方法二：  
创建文件`powershell_proxy.ps1`  
```powershell
# NOTE: registry keys for IE 8, may vary for other versions
$regPath = 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings'

function Clear-Proxy
{
    Set-ItemProperty -Path $regPath -Name ProxyEnable -Value 0
    Set-ItemProperty -Path $regPath -Name ProxyServer -Value ''
    Set-ItemProperty -Path $regPath -Name ProxyOverride -Value ''

    [Environment]::SetEnvironmentVariable('http_proxy', $null, 'User')
    [Environment]::SetEnvironmentVariable('https_proxy', $null, 'User')
}

function Set-Proxy
{
    $proxy = 'http://example.com'

    Set-ItemProperty -Path $regPath -Name ProxyEnable -Value 1
    Set-ItemProperty -Path $regPath -Name ProxyServer -Value $proxy
    Set-ItemProperty -Path $regPath -Name ProxyOverride -Value '<local>'

    [Environment]::SetEnvironmentVariable('http_proxy', $proxy, 'User')
    [Environment]::SetEnvironmentVariable('https_proxy', $proxy, 'User')
}
```