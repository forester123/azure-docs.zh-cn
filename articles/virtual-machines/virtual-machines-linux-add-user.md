---
title: "将用户添加到 Azure 上的 Linux VM | Microsoft Docs"
description: "将用户添加到 Azure 上的 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8aa633b-8b75-45d7-b61d-11ac112cedd5
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 3bb2032ed54ffc05214c771f17af81dd8b4864ab


---
# <a name="add-a-user-to-an-azure-vm"></a>将用户添加到 Azure VM
对于任何新的 Linux VM，首要任务之一就是创建新用户。  在本文中，将逐步创建 sudo 用户帐户、设置密码、添加 SSH 公钥，最后使用 `visudo` 来允许不带密码的 sudo。

先决条件包括：[Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)、[SSH 公钥与私钥](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、Azure 资源组、已安装 Azure CLI，并已使用 `azure config mode arm` 切换到 Azure Resource Manager 模式。

## <a name="quick-commands"></a>快速命令
```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## <a name="detailed-walkthrough"></a>详细演练
### <a name="introduction"></a>介绍
新服务器的最常见首要任务之一是添加用户帐户。  Root 登录应该禁用，root 帐户本身不应该用于 Linux 服务器，只有 sudo 可以。  使用 sudo 提升用户的 root 权限，才是管理和使用 Linux 的正确方式。

使用命令 `useradd` 将用户帐户添加到 Linux VM。  运行 `useradd` 修改 `/etc/passwd`、`/etc/shadow`、`/etc/group` 和 `/etc/gshadow`。  我们将在 `useradd` 命令中添加一个命令行标志，以便同时将新用户添加到 Linux 上的正确 sudo 组。  尽管 `useradd` 在 `/etc/passwd` 中创建条目，但不会为新用户帐户提供密码。  我们将使用简单的 `passwd` 命令为新用户创建初始密码。  最后一个步骤是修改 sudo 规则，以允许用户以 sudo 权限执行命令，而无需在执行每个命令时都输入密码。  由于该用户已使用私钥登录，我们可以推断该用户帐户并非想要图谋不轨，因此允许该用户在不使用密码的情况下进行 sudo 访问。  

### <a name="adding-a-single-sudo-user-to-an-azure-vm"></a>将单个 sudo 用户添加到 Azure VM
使用 SSH 密钥登录到 Azure VM。  如果未设置 SSH 公钥访问权限，请先阅读 [Using Public Key Authentication with Azure](http://link.to/article)（对 Azure 使用公钥身份验证）一文。  

`useradd` 命令将完成以下任务：

* 创建新用户帐户
* 以相同的名称创建新用户组
* 在 `/etc/passwd` 中添加空白条目
* 在 `/etc/gpasswd` 中添加空白条目

`-G` 命令行标志将新用户帐户添加到正确的 Linux 组，并为新用户帐户分配 root 提升权限。

#### <a name="add-the-user"></a>添加用户
```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### <a name="set-a-password"></a>设置密码
`useradd` 命令将创建用户，并向 `/etc/passwd` 和 `/etc/gpasswd` 同时添加条目，但不实际设置密码。  使用 `passwd` 命令将密码添加到条目。

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

现在，服务器上已有一个具有 sudo 权限的用户。

### <a name="add-your-ssh-public-key-to-the-new-user-account"></a>将 SSH 公钥添加到新用户帐户
在计算机上，结合新密码使用 `ssh-copy-id` 命令。

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### <a name="using-visudo-to-allow-sudo-usage-without-a-password"></a>通过 visudo 允许使用不带密码的 sudo
使用 `visudo` 编辑 `/etc/sudoers` 文件可添加多层保护，避免不当地修改这个重要文件。  执行 `visudo` 时将锁定 `/etc/sudoers` 文件，以确保没有任何其他用户能够在编辑文件时对文件进行更改。  尝试保存或退出时，`visudo` 还会检查 `/etc/sudoers` 文件中是否有错误，因此无法保存已损坏的 sudoers 文件。

我们已将用户添加到正确的默认组中以进行 sudo 访问。  现在我们要让这些组可以在没有密码的情况下使用 sudo。

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### <a name="verify-the-user-ssh-keys-and-sudo"></a>验证用户、ssh 密钥和 sudo
```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```



<!--HONumber=Nov16_HO3-->

