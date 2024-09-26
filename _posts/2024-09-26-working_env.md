---
title: 通过TailScale构建远程工作环境
date: 2024-09-26
categories: [Networking]
tags: [tailscale, ssh]     # TAG names should always be lowercase
description: 记录TailScale打洞和利用ssh进行远程开发的配置
---

## Tailscale

tailscale本身的用法已经很简单，指引清晰，记得取消主机器的Expiration，不再赘述。

## ssh配置

Windows ssh基本配置攻略很常见，不再赘述，下面记录遇到的一个问题

在配置好ssh服务和隧道后（即验证ssh密码连接成功），我试图通过配置公私钥对实现免密登录，显示错误

> Permission denied (publickey).

关闭服务端的服务，通过在控制台带-d选项启动，分析log，可以查看服务端从哪里获取了pubkey。在注释掉ssh_config最后两行后，可以确定是从.ssh/authorized_keys读取公钥。

在服务端log中出现关键错误

> debug1: trying public key file C:\\Users\\xxx\\.ssh/authorized_keys
> Fail publickey for xxx from xxx port xxx ssh2: ED25519 SHA256 ......

说明读取authorized_keys文件失败，最后确认是权限问题

```powershell
$acl = Get-Acl C:\Users\xxx\.ssh\authorized_keys
$acl.SetAccessRuleProtection($true, $false)
$administratorsRule = New-Object system.security.accesscontrol.filesystemaccessrule("Administrators","FullControl","Allow")
$systemRule = New-Object system.security.accesscontrol.filesystemaccessrule("SYSTEM","FullControl","Allow")
$acl.SetAccessRule($administratorsRule)
$acl.SetAccessRule($systemRule)
$acl | Set-Acl
```

这段powershell代码设置了keyfile的SYSTEM和Admin权限，除此之外，还在文件属性中调整实际使用的用户的权限，设置为修改、读写、执行。

在做完上述设置后，可以验证免密登录。

参考
[authentication problem](https://serverfault.com/questions/1032713/public-key-authentication-not-work-on-windows-10-professional)
