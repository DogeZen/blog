---
title : windows wsl2配置
date: 2021-07-1 08:00:00
tags:  
- wsl2
- 部署
---

## 更新windows，以支持wsl2

检查 Windows 版本及内部版本号， Win+ R，输入“winver”，选择“确定”

- 对于 x64 系统： **版本 1903** 或更高版本，采用 **内部版本 18362** 或更高版本。
- 对于 ARM64 系统： **版本 2004** 或更高版本，采用 **内部版本 19041** 或更高版本。

低于对应版本，则去打开windows预览体验计划(**这步是为了之后cuda，会要求重启，耐心等待即可**)



打开高级选项中的接收其他microsoft其他产品更新，然后确保windows更新到最新

## 启动虚拟功能

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

## 下载 自己系统的Linux 内核更新包

[适用于 x64 计算机的 WSL2 ](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)[Linux ](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)[内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

 [ARM64 包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_arm64.msi)

## 管理员权限运行powershell

输入

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

等待重新启动计算机，然后继续执行下一步

## 把wsl升级到wsl2

```
wsl --set-default-version 2
```
## 打开ubuntu
进微软商店搜WSL

选择ubuntu，点获取(这里推荐18.04)

点击运行，设置完用户名密码之后就能正常使用了