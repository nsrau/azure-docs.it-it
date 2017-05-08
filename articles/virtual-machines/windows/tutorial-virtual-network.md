---
title: Reti virtuali di Azure e macchine virtuali Windows | Microsoft Docs
description: 'Esercitazione: gestire reti virtuali di Azure e macchine virtuali Windows con Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 7015a5317c631dad9079f2694051fa7fb28d232b
ms.lasthandoff: 04/26/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Gestire reti virtuali di Azure e macchine virtuali Windows con Azure PowerShell

Questa esercitazione illustra la creazione di più macchine virtuali (VM) in una rete virtuale e la configurazione della connettività di rete tra di esse. Al termine, una VM "front-end" sarà accessibile da Internet sulla porta 80 per connessioni HTTP. Una VM "back-end" con un database SQL Server sarà isolata e accessibile solo dalla VM front-end sulla porta 1433.

Le procedure descritte in questa esercitazione possono essere completate usando il modulo [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) più recente.

## <a name="create-vnet"></a>Creare la rete virtuale

Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. All'interno di una rete virtuale si trovano subnet, regole per la connettività a tali subnet e connessioni dalle VM alle subnet.

Per poter creare qualsiasi altra risorsa di Azure, è prima di tutto necessario creare un gruppo di risorse con [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-3.8.0). Nell'esempio seguente viene creato un gruppo di risorse denominato `myRGNetwork` nella località `westus`:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location westus
```

Una subnet è una risorsa figlio di una rete virtuale e consente di definire i segmenti degli spazi di indirizzi all'interno di un blocco CIDR, usando i prefissi degli indirizzi IP. Le NIC possono essere aggiunte alle subnet e connesse alle macchine virtuali, fornendo connettività per diversi carichi di lavoro.

Creare una subnet con [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0):

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Creare una rete virtuale denominata `myVNet` con `myFrontendSubnet` usando [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork?view=azurermps-3.8.0):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>Creare la VM front-end

Per comunicare in una rete virtuale, una VM deve avere un'interfaccia di rete (NIC) virtuale. `myFrontendVM` deve essere accessibile da Internet e deve quindi avere anche un indirizzo IP pubblico. 

Creare un indirizzo IP pubblico con [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress?view=azurermps-3.8.0):

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

Creare un'interfaccia di rete con [New AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface?view=azurermps-3.8.0):


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Impostare il nome utente e la password necessari per l'account amministratore della VM con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Creare le VM con [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-3.8.0), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem?view=azurermps-3.8.0), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage?view=azurermps-3.8.0), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-3.8.0), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?view=azurermps-3.8.0) e [New-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm?view=azurermps-3.8.0). 

```powershell
$frontendVM = New-AzureRmVMConfig -VMName myFrontendVM -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem -VM $frontendVM -Windows -ComputerName myFrontendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage -VM $frontendVM -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
$frontendVM = Set-AzureRmVMOSDisk -VM $frontendVM -Name myFrontendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface -VM $frontendVM -Id $frontendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $frontendVM
```

## <a name="install-web-server"></a>Installare il server Web

È possibile installare IIS in `myFrontendVM` usando una sessione Desktop remoto. Per accedere alla VM è necessario ottenerne l'indirizzo IP pubblico.

È possibile ottenere l'indirizzo IP pubblico di `myFrontendVM` con [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-3.8.0). L'esempio seguente ottiene l'indirizzo IP `myPublicIPAddress` creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myRGNetwork -Name myPublicIPAddress | select IpAddress
```

Prendere nota di questo indirizzo IP per poterlo usare nei passaggi successivi.

Usare il comando seguente per creare una sessione Desktop remoto con `myFrontendVM`. Sostituire `<publicIPAddress>` con l'indirizzo registrato in precedenza. Quando richiesto, immettere le credenziali usate durante la creazione della VM.

```
mstsc /v:<publicIpAddress>
``` 

Dopo aver eseguito l'accesso a `myFrontendVM`, è possibile usare una singola riga di codice di PowerShell per installare IIS e abilitare la regola del firewall locale per consentire il traffico Web. Aprire un prompt di PowerShell ed eseguire questo comando:

Usare [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) per eseguire l'estensione di script personalizzata che installa il server Web IIS:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

È ora possibile usare l'indirizzo IP pubblico per passare alla VM e visualizzare il sito IIS.

![Sito IIS predefinito](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>Gestire il traffico interno

Un gruppo di sicurezza di rete (NSG) contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole interfacce di rete collegate a VM. Per aprire o chiudere l'accesso alle VM tramite le porte vengono usate le regole dei gruppi di sicurezza di rete. Alla creazione di `myFrontendVM`, la porta in ingresso 3389 è stata aperta automaticamente per la connettività RDP.

La comunicazione interna delle VM può essere configurata usando un gruppo di sicurezza di rete. Questa sezione illustra come creare una subnet aggiuntiva nella rete e assegnare un gruppo di sicurezza di rete a tale subnet per consentire una connessione da `myFrontendVM` a `myBackendVM` sulla porta 1433. La subnet viene quindi assegnata alla VM al momento della creazione.

È possibile limitare il traffico interno verso `myBackendVM` solo da `myFrontendVM` creando un gruppo di sicurezza di rete per la subnet back-end. L'esempio seguente crea una regola del gruppo di sicurezza di rete denominata `myBackendNSGRule` con [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig?view=azurermps-3.8.0):

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Aggiungere un gruppo di sicurezza di rete denominato `myBackendNSG` con [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecuritygroup?view=azurermps-3.8.0):

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>Aggiungere la subnet back-end

Aggiungere `myBackEndSubnet` a `myVNet` con [Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0):

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myRGNetwork -Name myVNet
```

## <a name="create-back-end-vm"></a>Creare la VM back-end

Il modo più semplice per creare la VM back-end consiste nell'usare un'immagine di SQL Server. Questa esercitazione si limita a creare la VM con il server di database e non include informazioni sull'accesso al database.

Creare `myBackendNic`:

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Impostare il nome utente e la password necessari per l'account amministratore della VM con Get-Credential:

```powershell
$cred = Get-Credential
```

Creare `myBackendVM`:

```powershell
$backendVM = New-AzureRmVMConfig -VMName myBackendVM -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem -VM $backendVM -Windows -ComputerName myBackendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage -VM $backendVM -PublisherName MicrosoftSQLServer -Offer SQL2016-WS2016 -Skus Enterprise -Version latest
$backendVM = Set-AzureRmVMOSDisk -VM $backendVM -Name myBackendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface -VM $backendVM -Id $backendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $backendVM
```

L'immagine, in cui è installato SQL Server, non viene usata in questa esercitazione. È inclusa per illustrare come è possibile configurare una VM per il traffico Web e una VM per la gestione di database.


