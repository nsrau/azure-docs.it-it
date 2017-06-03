---
title: Personalizzare una macchina virtuale Windows in Azure | Microsoft Docs
description: Informazioni su come usare l&quot;estensione script personalizzata e Key Vault per personalizzare le macchine virtuali Windows in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 014d282daffdbfc03e7f3495f8e59bfda4cdb396
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Come personalizzare una macchina virtuale Windows in Azure
Per configurare macchine virtuali (VM) in modo rapido e coerente, di norma è consigliabile una qualche forma di automazione. Un approccio comune per personalizzare una macchina virtuale Windows consiste nell'usare l'[estensione script personalizzata per Windows](extensions-customscript.md). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per installare IIS
> * Creare una macchina virtuale che usi l'estensione dello script personalizzata
> * Visualizzare un sito IIS in esecuzione dopo l'applicazione dell'estensione

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="custom-script-extension-overview"></a>Panoramica dell'estensione script personalizzata
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub, oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

È possibile usare l'estensione script personalizzata con macchine virtuali sia Linux che Windows.


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Per poter creare una macchina virtuale è prima necessario creare un gruppo di risorse con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAutomate* nella posizione *EastUS*:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location EastUS
```

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

A questo punto è possibile creare la macchina virtuale con il comando [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Nell'esempio seguente vengono creati i componenti della rete virtuale necessari e viene eseguita la configurazione del sistema operativo. Viene quindi creata una macchina virtuale denominata *myVM*:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location EastUS -VM $vmConfig
```

Per creare le risorse e la macchina virtuale sono necessari alcuni minuti.


## <a name="automate-iis-install"></a>Automatizzare l'installazione IIS
Usare il comando [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) per installare l'estensione dello script personalizzata. L'estensione esegue `powershell Add-WindowsFeature Web-Server` per installare il server Web IIS e quindi aggiorna la pagina *Default.htm* per visualizzare il nome host della VM:

```powershell
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
```


## <a name="test-web-site"></a>Testare il sito Web
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Nell'esempio seguente si ottiene l'indirizzo IP per *myPublicIP* creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Name myPublicIP | select IpAddress
```

Sarà quindi possibile immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzato il sito Web, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico, come nell'esempio seguente:

![Esecuzione del sito Web IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata automatizzata l'installazione di IIS in una macchina virtuale. Si è appreso come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per installare IIS
> * Creare una macchina virtuale che usi l'estensione dello script personalizzata
> * Visualizzare un sito IIS in esecuzione dopo l'applicazione dell'estensione

Passare all'esercitazione successiva per imparare a creare immagini di macchine virtuali personalizzate.

> [!div class="nextstepaction"]
> [Creare un'immagine di VM personalizzata](./tutorial-custom-images.md)

