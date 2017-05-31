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
ms.date: 05/02/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7fd0ace35cfe0286c874e4a75b733053aa945d39
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Gestire reti virtuali di Azure e macchine virtuali Windows con Azure PowerShell

Le macchine virtuali di Azure usano la rete di Azure per la comunicazione di rete interna ed esterna. In questa esercitazione vengono create più macchine virtuali (VM) in una rete virtuale e viene configurata la connettività di rete tra di esse. Si apprenderà come:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare subnet di reti virtuali
> * Controllare il traffico di rete con gruppi di sicurezza di rete
> * Visualizzare le regole del traffico applicate

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-vnet"></a>Creare la rete virtuale

Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. All'interno di una rete virtuale si trovano subnet, regole per la connettività a tali subnet e connessioni dalle VM alle subnet.

Per poter creare qualsiasi altra risorsa di Azure, è necessario prima creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myRGNetwork* nella posizione *EastUS*:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Una subnet è una risorsa figlio di una rete virtuale e consente di definire i segmenti degli spazi di indirizzi all'interno di un blocco CIDR, usando i prefissi degli indirizzi IP. Le NIC possono essere aggiunte alle subnet e connesse alle macchine virtuali, fornendo connettività per diversi carichi di lavoro.

Creare una subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Creare una rete virtuale denominata *myVNet* che usa *myFrontendSubnet* con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>Creare la VM front-end

Per comunicare in una rete virtuale, una VM deve avere un'interfaccia di rete (NIC) virtuale. *myFrontendVM* deve essere accessibile da Internet e deve quindi avere anche un indirizzo IP pubblico. 

Creare un indirizzo IP pubblico con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

Creare un'interfaccia di rete con [New AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Impostare il nome utente e la password necessari per l'account amministratore della VM con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Creare le VM con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) e [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). 

```powershell
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="install-web-server"></a>Installare il server Web

È possibile installare IIS in *myFrontendVM* usando una sessione Desktop remoto. Per accedere alla VM è necessario ottenerne l'indirizzo IP pubblico.

È possibile ottenere l'indirizzo IP pubblico di *myFrontendVM* con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L'esempio seguente ottiene l'indirizzo IP per *myPublicIPAddress* creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

Prendere nota di questo indirizzo IP per poterlo usare nei passaggi successivi.

Usare il comando seguente per creare una sessione Desktop remoto con *myFrontendVM*. Sostituire *<publicIPAddress>* con l'indirizzo registrato in precedenza. Quando richiesto, immettere le credenziali usate durante la creazione della VM.

```
mstsc /v:<publicIpAddress>
``` 

Dopo avere eseguito l'accesso a *myFrontendVM*, è possibile usare una singola riga di codice di PowerShell per installare IIS e abilitare la regola del firewall locale per consentire il traffico Web. Aprire un prompt di PowerShell ed eseguire questo comando:

Usare [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) per eseguire l'estensione di script personalizzata che installa il server Web IIS:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

È ora possibile usare l'indirizzo IP pubblico per passare alla VM e visualizzare il sito IIS.

![Sito IIS predefinito](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>Gestire il traffico interno

Un gruppo di sicurezza di rete (NSG) contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole interfacce di rete collegate a VM. Per aprire o chiudere l'accesso alle VM tramite le porte vengono usate le regole dei gruppi di sicurezza di rete. Alla creazione di *myFrontendVM*, la porta in ingresso 3389 è stata aperta automaticamente per la connettività RDP.

La comunicazione interna delle VM può essere configurata usando un gruppo di sicurezza di rete. Questa sezione illustra come creare una subnet aggiuntiva nella rete e assegnare un gruppo di sicurezza di rete a tale subnet per consentire una connessione da *myFrontendVM* a *myBackendVM* sulla porta 1433. La subnet viene quindi assegnata alla VM al momento della creazione.

È possibile limitare il traffico interno verso *myBackendVM* solo da *myFrontendVM* creando un gruppo di sicurezza di rete per la subnet back-end. L'esempio seguente crea una regola del gruppo di sicurezza di rete denominata *myBackendNSGRule* con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

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

Aggiungere un gruppo di sicurezza di rete denominato *myBackendNSG* con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>Aggiungere la subnet back-end

Aggiungere *myBackEndSubnet* a *myVNet* con [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig):

```powershell
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

## <a name="create-back-end-vm"></a>Creare la VM back-end

Il modo più semplice per creare la VM back-end consiste nell'usare un'immagine di SQL Server. Questa esercitazione si limita a creare la VM con il server di database e non include informazioni sull'accesso al database.

Creare *myBackendNic*:

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Impostare il nome utente e la password necessari per l'account amministratore della VM con Get-Credential:

```powershell
$cred = Get-Credential
```

Creare *myBackendVM*:

```powershell
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

L'immagine, in cui è installato SQL Server, non viene usata in questa esercitazione. È inclusa per illustrare come è possibile configurare una VM per il traffico Web e una VM per la gestione di database.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state create e protette reti di Azure in relazione a macchine virtuali. 

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare subnet di reti virtuali
> * Controllare il traffico di rete con gruppi di sicurezza di rete
> * Visualizzare le regole del traffico applicate

Passare all'esercitazione successiva per informazioni sul monitoraggio della protezione dei dati nelle macchine virtuali con Backup di Azure. .

> [!div class="nextstepaction"]
> [Eseguire il backup di macchine virtuali Windows in Azure](./tutorial-backup-vms.md)

