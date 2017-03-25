---
title: Avvio rapido in Azure - Creare VM con PowerShell | Documentazione Microsoft
description: Informazioni veloci su come creare una macchina virtuale Linux con PowerShell
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: f75df3258a0be564de827b15cec160b82b004817
ms.lasthandoff: 03/14/2017


---

# <a name="create-a-linux-virtual-machine-with-powershell"></a>Creare una macchina virtuale Linux con PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida descrive dettagliatamente l'uso di PowerShell per creare una macchina virtuale di Azure che esegue Ubuntu 14.04 LTS.

Prima di iniziare, è necessario archiviare una chiave pubblica SSH con il nome `id_rsa.pub` nella directory `.ssh` del profilo utente di Windows. Per informazioni dettagliate sulla creazione delle chiavi SSH per Azure, vedere [Create SSH keys for Azure](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Creare chiavi SSH per Azure).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-networking-resources"></a>Creare risorse di rete

Creare una rete virtuale, una subnet e un indirizzo IP pubblico. Queste risorse vengono usate per fornire la connettività di rete alla macchina virtuale e connetterla a Internet.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location westeurope `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location westeurope `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Creare un gruppo di sicurezza di rete e una regola del gruppo di sicurezza di rete. Il gruppo di sicurezza di rete protegge la macchina virtuale usando le regole in entrata e in uscita. In questo caso viene creata una regola in entrata per la porta 22 che consente le connessioni SSH in ingresso.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH
```

Creare una scheda di rete per la macchina virtuale. La scheda di rete connette la macchina virtuale a una subnet, a un gruppo di sicurezza di rete e a un indirizzo IP pubblico.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare una configurazione di macchina virtuale. Questa configurazione include le impostazioni utilizzate quando si distribuisce la macchina virtuale, ad esempio l'immagine della macchina virtuale, la dimensione e la configurazione di autenticazione.

```powershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 14.04.2-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Creare la macchina virtuale.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Dopo aver completato la distribuzione, creare una connessione SSH con la macchina virtuale.

Eseguire i comandi seguenti per restituire l'indirizzo IP pubblico della macchina virtuale.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Da un sistema con SSH installato, usare il comando seguente per connettersi alla macchina virtuale. Se si lavora in Windows, è possibile usare [Putty](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-ssh-from-windows?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-private-key-for-putty) per creare la connessione. 

```bash 
ssh <Public IP Address>
```

Quando viene richiesto, il nome utente di accesso è `azureuser`. Se è stata immessa una passphrase durante la creazione delle chiavi SSH, sarà necessario immettere anche questa.

## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non servono più, il comando seguente consente di rimuovere il gruppo di risorse, la VM e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Creare esercitazioni per macchine virtuali a disponibilità elevata](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Esplorare gli esempi di distribuzione delle VM con PowerShell](./virtual-machines-linux-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

