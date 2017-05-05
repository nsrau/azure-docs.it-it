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
ms.date: 04/19/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: f8ce553dc528fa98fcffd42750da6bec72266129
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Come personalizzare una macchina virtuale Windows in Azure
Per configurare macchine virtuali (VM) in modo rapido e coerente, di norma è consigliabile una qualche forma di automazione. Un approccio comune per personalizzare una macchina virtuale Windows consiste nell'usare l'[estensione script personalizzata per Windows](extensions-customscript.md). Questa esercitazione illustra come usare l'estensione script personalizzata per installare e configurare IIS per l'esecuzione di un sito Web di base.

La procedura descritta in questa esercitazione può essere completata usando il modulo [Azure PowerShell](/powershell/azureps-cmdlets-docs/) più recente.


## <a name="custom-script-extension-overview"></a>Panoramica dell'estensione script personalizzata
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub, oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

È possibile usare l'estensione script personalizzata con macchine virtuali sia Linux che Windows.


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Per poter creare una macchina virtuale è prima necessario creare un gruppo di risorse con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroupAutomate` nella località `westus`:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location westus
```

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

A questo punto è possibile creare la macchina virtuale con il comando [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Nell'esempio seguente vengono creati i componenti necessari della rete virtuale e viene eseguita la configurazione del sistema operativo. Viene quindi creata una macchina virtuale denominata `myVM`:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAutomate -Location westus `
-Name myVnet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupAutomate -Location westus `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroupAutomate -Location westus `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroupAutomate -Location westus `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location westus -VM $vmConfig
```

Per creare le risorse e la macchina virtuale sono necessari alcuni minuti.


## <a name="automate-iis-install"></a>Automatizzare l'installazione IIS
Usare il comando [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) per installare l'estensione script personalizzata. L'estensione esegue `powershell Add-WindowsFeature Web-Server` per installare il server Web IIS, quindi aggiorna la pagina `Default.htm` per visualizzare il nome host della macchina virtuale:

```powershell
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location westus
```


## <a name="test-web-site"></a>Testare il sito Web
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L'esempio seguente ottiene l'indirizzo IP `myPublicIP` creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAutomate -Name myPublicIP | select IpAddress
```

Sarà quindi possibile immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzato il sito Web, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico, come nell'esempio seguente:

![Esecuzione del sito Web IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come personalizzare una macchina virtuale con l'estensione script personalizzata. Passare all'esercitazione successiva per imparare a creare un sito Web IIS con bilanciamento del carico.

[Bilanciare il carico di macchine virtuali](./tutorial-load-balancer.md)
