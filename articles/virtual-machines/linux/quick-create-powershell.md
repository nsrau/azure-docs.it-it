---
title: Guida introduttiva - Creare una macchina virtuale Linux con Azure PowerShell | Microsoft Docs
description: In questa guida introduttiva si apprenderà come usare Azure PowerShell per creare una macchina virtuale Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 46ab5cae7514adfc4ec31ad88f5445a09e3c0e6a
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55975297"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Avvio rapido: Creare una macchina virtuale Linux in Azure con PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida introduttiva illustra come usare il modulo Azure PowerShell per distribuire una macchina virtuale (VM) Linux in Azure. In questa guida introduttiva si usa l'immagine di Ubuntu 16.04 LTS di Canonical disponibile nel Marketplace. Per vedere in azione la macchina virtuale, viene stabilita anche una connessione SSH alla macchina virtuale e viene installato il server Web NGINX.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="create-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Per completare questa guida introduttiva è necessaria una coppia di chiavi SSH. Se si ha già una coppia di chiavi SSH, ignorare questo passaggio.

Aprire una shell Bash e usare [ssh-keygen](https://www.ssh.com/ssh/keygen/) per creare una coppia di chiavi SSH. Se non si dispone di una shell Bash nel computer locale, è possibile usare [Azure Cloud Shell](https://shell.azure.com/bash).  

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Per informazioni più dettagliate su come creare coppie di chiavi SSH, incluso l'uso di PuTTy, vedere [Come usare le chiavi SSH con Windows in Azure](ssh-from-windows.md).

Se si crea la coppia di chiavi SSH usando Cloud Shell, questa verrà archiviata in un'immagine del contenitore in un [account di archiviazione creato automaticamente da Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Non eliminare l'account di archiviazione o la condivisione file presente nell'account prima di aver recuperato le chiavi. In caso contrario si perderà l'accesso alla macchina virtuale. 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Creare risorse di rete virtuale

Creare una rete virtuale, una subnet e un indirizzo IP pubblico. Queste risorse vengono usate per offrire la connettività di rete alla macchina virtuale e connetterla a Internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Creare un gruppo di sicurezza di rete di Azure e una regola del traffico. Il gruppo di sicurezza di rete protegge la macchina virtuale usando le regole in entrata e in uscita. Nell'esempio seguente viene creata una regola in ingresso per la porta TCP 22 che consente le connessioni SSH. Per consentire il traffico Web in ingresso, viene creata anche una regola in ingresso per la porta TCP 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Creare una scheda di interfaccia di rete virtuale con [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). La scheda di interfaccia di rete virtuale connette la macchina virtuale a una subnet, a un gruppo di sicurezza di rete e a un indirizzo IP pubblico.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per creare una macchina virtuale in PowerShell, creare una configurazione con impostazioni quali l'immagine da usare, le dimensioni e le opzioni di autenticazione. La configurazione verrà quindi usata durante la creazione della macchina virtuale.

Definire le credenziali SSH, le informazioni sul sistema operativo e le dimensioni della macchina virtuale. In questo esempio la chiave SSH viene archiviata in `~/.ssh/id_rsa.pub`. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

A questo punto combinare le definizioni di configurazione precedenti per la creazione con [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm):

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

La distribuzione della macchina virtuale richiederà alcuni minuti. Al termine della distribuzione, passare alla sezione successiva.

## <a name="connect-to-the-vm"></a>Connettersi alla VM

Creare una connessione SSH con la macchina virtuale usando l'indirizzo IP pubblico. Per visualizzare l'indirizzo IP pubblico della VM usare il cmdlet [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress):

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Con la stessa shell Bash usata per creare la coppia di chiavi SSH (ad esempio [Azure Cloud Shell](https://shell.azure.com/bash) o la shell Bash locale), incollare il comando di connessione SSH nella shell per creare una sessione SSH.

```bash
ssh azureuser@10.111.12.123
```

Quando viene richiesto, il nome utente di accesso è *azureuser*. Se con le chiavi SSH viene usata una passphrase, è necessario immetterla quando viene richiesta.


## <a name="install-nginx"></a>Installare NGINX

Per visualizzare la macchina virtuale in azione, installare il server Web NGINX. Dalla sessione SSH, aggiornare le origini dei pacchetti e quindi installare il pacchetto NGINX più recente.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Al termine, digitare `exit` per uscire dalla sessione SSH.


## <a name="view-the-web-server-in-action"></a>Visualizzare il server Web in azione

Usare il Web browser che si preferisce per vedere la pagina iniziale di NGINX predefinita. Immettere l'indirizzo IP pubblico della macchina virtuale come indirizzo Web. L'indirizzo IP pubblico è reperibile nella pagina di panoramica della macchina virtuale o come parte della stringa di connessione SSH usata in precedenza.

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse, la VM e tutte le risorse correlate con il cmdlet [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, sono stati creati un gruppo di sicurezza di rete e una regola ed è stato installato un server Web. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Linux.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Linux in Azure](./tutorial-manage-vm.md)
