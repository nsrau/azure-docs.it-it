---
title: Guida introduttiva - Creare una macchina virtuale Linux con Azure PowerShell | Microsoft Docs
description: In questa guida introduttiva si apprenderà come usare Azure PowerShell per creare una macchina virtuale Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e910ced35738fcba27a8a1d7f2f010b2cd42e55d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188991"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Guida introduttiva: creare una macchina virtuale Linux in Azure con PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida introduttiva illustra come usare il modulo Azure PowerShell per distribuire una macchina virtuale Linux in Azure che esegue Ubuntu. Per vedere la macchina virtuale in azione, eseguire SSH nella macchina virtuale e installare il server Web NGINX.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

È infine necessario archiviare una chiave pubblica SSH denominata *id_rsa.pub* nella directory *.ssh* del profilo utente di Windows. Per informazioni dettagliate su come creare e usare le chiavi SSH, vedere [Creare chiavi SSH per Azure](ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Creare risorse di rete virtuale

Creare una rete virtuale, una subnet e un indirizzo IP pubblico. Queste risorse vengono usate per offrire la connettività di rete alla macchina virtuale e connetterla a Internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Creare un gruppo di sicurezza di rete di Azure e una regola del traffico. Il gruppo di sicurezza di rete protegge la macchina virtuale usando le regole in entrata e in uscita. Nell'esempio seguente viene creata una regola in ingresso per la porta TCP 22 che consente le connessioni SSH. Per consentire il traffico Web in ingresso, viene creata anche una regola in ingresso per la porta TCP 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Creare una scheda di interfaccia di rete virtuale con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). La scheda di interfaccia di rete virtuale connette la macchina virtuale a una subnet, a un gruppo di sicurezza di rete e a un indirizzo IP pubblico.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

La configurazione di una macchina virtuale include le impostazioni usate quando si distribuisce una macchina virtuale, ad esempio un'immagine della macchina virtuale, la dimensione e le opzioni di autenticazione. Definire le credenziali SSH, le informazioni sul sistema operativo e le dimensioni della macchina virtuale come indicato di seguito:

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

A questo punto combinare le definizioni di configurazione precedenti per avviare la creazione con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Al termine della distribuzione eseguire SSH nella macchina virtuale. Per vedere la macchina virtuale in azione, viene installato il server Web NGINX.

Per vedere l'indirizzo IP pubblico della macchina virtuale usare il cmdlet [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Usare un client SSH per la connessione alla macchina virtuale. È possibile usare Azure Cloud Shell da un Web browser oppure, se si usa Windows, è possibile usare [Putty](ssh-from-windows.md) o il [sottosistema di Windows per Linux](/windows/wsl/install-win10). Indicare l'indirizzo IP pubblico della macchina virtuale:

```bash
ssh azureuser@IpAddress
```

Quando viene richiesto, il nome utente di accesso è *azureuser*. Se con le chiavi SSH viene usata una passphrase, è necessario immetterla quando viene richiesta.

## <a name="install-web-server"></a>Installare il server Web

Per vedere la macchina virtuale in azione, installare il server Web NGINX. Per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente, eseguire i comandi seguenti dalla sessione SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Al termine `exit` dalla sessione SSH

## <a name="view-the-web-server-in-action"></a>Visualizzare il server Web in azione

Dopo l'installazione di NGINX e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la home page predefinita di NGINX. Usare l'indirizzo IP pubblico della macchina virtuale ottenuto nel passaggio precedente. L'esempio seguente mostra il sito Web predefinito di NGINX:

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, sono stati creati una regola e un gruppo di sicurezza di rete ed è stato installato un server Web di base. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Linux.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Linux in Azure](./tutorial-manage-vm.md)
