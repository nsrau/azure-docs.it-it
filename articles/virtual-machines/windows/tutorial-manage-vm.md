---
title: Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell | Microsoft Docs
description: 'Esercitazione: creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2237f2e5cb67df019d0975e764602babe7f4c8f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell

Le macchine virtuali di Azure offrono un ambiente di elaborazione completamente configurabile e flessibile. Questa esercitazione illustra gli elementi di base della distribuzione di una macchina virtuale di Azure, ad esempio la selezione delle dimensioni di una VM, la selezione dell'immagine di una VM e la distribuzione di una VM. Si apprenderà come:

> [!div class="checklist"]
> * Creare e connettersi a una macchina virtuale
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare macchine virtuali di dimensioni specifiche
> * Ridimensionare una VM
> * Visualizzare e comprendere lo stato di una macchina virtuale


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 3.6 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroupVM* nell'area *EastUS*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

Il gruppo di risorse viene specificato quando si crea o si modifica una VM, come viene illustrato in questa esercitazione.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Una macchina virtuale deve essere connessa a una rete virtuale. Per comunicare con la macchina virtuale viene usato un indirizzo IP pubblico tramite una scheda di interfaccia di rete.

### <a name="create-virtual-network"></a>Creare una rete virtuale

Creare una subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Creare un indirizzo IP pubblico

Creare un indirizzo IP pubblico con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Creare una scheda di interfaccia di rete

Creare una scheda di interfaccia di rete con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Un [gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md) di Azure consente di controllare il traffico in ingresso e in uscita per una o più macchine virtuali. Le regole di un gruppo di sicurezza di rete consentono o impediscono il traffico di rete su una porta specifica o un intervallo di porte. Queste regole possono includere un prefisso dell'indirizzo di origine, in modo che solo il traffico proveniente da un'origine specificata possa comunicare con una macchina virtuale. Per accedere al server Web IIS che si sta installando, è necessario aggiungere una regola in ingresso per il gruppo di sicurezza di rete.

Per creare una regola in ingresso per il gruppo di sicurezza di rete, usare [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig). L'esempio seguente crea una regola per il gruppo di sicurezza di rete denominata *myNSGRule* che apre la porta *3389* per la macchina virtuale:

```azurepowershell-interactive
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Creare un gruppo di sicurezza di rete usando *myNSGRule* con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

Aggiungere il gruppo di sicurezza di rete alla subnet della rete virtuale con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

Aggiornare la rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```azurepowershell-interactive
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Crea macchina virtuale

Per la creazione di una macchina virtuale sono disponibili diverse opzioni, ad esempio l'immagine del sistema operativo, il ridimensionamento del disco e le credenziali amministrative. In questo esempio viene creata una macchina virtuale con un nome *myVM* che esegue la versione più recente di Windows Server 2016 Datacenter.

Impostare il nome utente e la password necessari per l'account amministratore della macchina virtuale con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare la configurazione iniziale per la macchina virtuale con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig):

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Aggiungere le informazioni sul sistema operativo nella configurazione della macchina virtuale con [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem):

```azurepowershell-interactive
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Aggiungere le informazioni sull'immagine alla configurazione della macchina virtuale con [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage):

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

Aggiungere le impostazioni del disco del sistema operativo alla configurazione della macchina virtuale con [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk):

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

Aggiungere la scheda di interfaccia di rete creata precedentemente alla configurazione della macchina virtuale con [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```azurepowershell-interactive
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Creare la macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>Connettersi alla macchina virtuale

Dopo aver completato la distribuzione, creare una connessione desktop remoto con la macchina virtuale.

Eseguire i comandi seguenti per restituire l'indirizzo IP pubblico della macchina virtuale. Annotare questo indirizzo IP, in modo da potersi connettere ad esso con il browser per testare la connettività Web in un passaggio futuro.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

Usare il comando seguente nel computer locale per creare una sessione desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo *publicIPAddress* della macchina virtuale. Quando richiesto, immettere le credenziali utilizzate durante la creazione della macchina virtuale.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Informazioni sulle immagini delle VM

Azure Marketplace include diverse immagini di macchine virtuali che possono essere usate per creare nuove VM. Nei passaggi precedenti è stata creata una macchina virtuale usando un'immagine Windows Server 2016-Datacenter. In questo passaggio, viene usato il modulo PowerShell per cercare nel marketplace altre immagini di Windows da usare anche come base per le nuove macchine virtuali. Questo processo consiste nell'individuazione del server di pubblicazione, dell'offerta e del nome dell'immagine (Sku). 

Usare il comando [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) per restituire un elenco di server di pubblicazione di immagini.  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Usare [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) per restituire un elenco di offerte delle immagini. Con questo comando, l'elenco restituito viene filtrato nel server di pubblicazione specificato. 

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

Il comando [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) verrà quindi filtrato in base al nome del server di pubblicazione e dell'offerta per restituire un elenco di nomi di immagini.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

È possibile usare queste informazioni per distribuire una macchina virtuale con un'immagine specifica. In questo esempio viene impostato il nome dell'immagine per l'oggetto della macchina virtuale. Fare riferimento agli esempi precedenti in questa esercitazione per la procedura di distribuzione completa.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>Informazioni sulle dimensioni delle VM

La dimensioni di una macchina virtuale determinano la quantità di risorse di calcolo, ad esempio CPU, GPU e memoria, disponibili per la macchina virtuale. Le macchine virtuali devono essere create con dimensioni adeguate al carico di lavoro previsto. Se aumenta il carico di lavoro, è possibile ridimensionare una macchina virtuale esistente.

### <a name="vm-sizes"></a>Dimensioni delle VM

La tabella seguente classifica le dimensioni a seconda dei casi d'uso.  

| Tipo                     | Dimensioni           |    Descrizione       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Scopo generico         |DSv2, Dv2, DS, D, Av2, A0-7| Rapporto equilibrato tra CPU e memoria. Soluzione ideale per sviluppo o test e soluzioni di dati e applicazioni medio-piccole.  |
| Ottimizzate per il calcolo      | Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per applicazioni con livelli medi di traffico, dispositivi di rete e processi batch.        |
| Ottimizzate per la memoria       | GS, G, DSv2, DS, Dv2, D   | Rapporto elevato tra memoria e core. Soluzione ideale per database relazionali, cache medio-grandi e analisi in memoria.                 |
| Ottimizzate per l'archiviazione       | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| GPU           | NV, NC            | VM specializzate ottimizzate per livelli intensivi di rendering della grafica ed editing di video.       |
| Prestazioni elevate | H, A8-11          | Le VM con CPU più potenti, con interfacce di rete ad alta velocità effettiva opzionali (RDMA). 


### <a name="find-available-vm-sizes"></a>Trovare le dimensioni delle macchine virtuali disponibili

Per visualizzare un elenco delle dimensioni delle VM disponibili in una determinata area, usare il comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>Ridimensionare una VM

Dopo la distribuzione di una VM, è possibile ridimensionarla per aumentare o ridurre l'allocazione delle risorse.

Prima di ridimensionare una macchina virtuale, verificare che le dimensioni desiderate siano disponibili nel cluster della VM corrente. Il comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) restituisce un elenco di dimensioni. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

Se le dimensioni desiderate sono disponibili, la VM può essere ridimensionata mentre è accesa, ma durante l'operazione viene riavviata.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

Se nel cluster corrente non sono disponibili le dimensioni desiderate, è necessario deallocare la VM prima di poter eseguire l'operazione di ridimensionamento. Si noti che, quando la macchina virtuale viene riaccesa, vengono rimossi tutti i dati sul disco temporaneo, mentre l'indirizzo IP pubblico cambia a meno che non venga usato un indirizzo IP statico. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>Stati di alimentazione di una macchina virtuale

Una macchina virtuale di Azure può avere uno dei diversi stati di alimentazione. Questo stato rappresenta lo stato corrente della VM dal punto di vista dell'hypervisor. 

### <a name="power-states"></a>Stati di alimentazione

| Stato di alimentazione | Descrizione
|----|----|
| Avvio in corso | Indica che è in corso l'avvio della macchina virtuale. |
| In esecuzione | Indica che la macchina virtuale è in esecuzione. |
| Arresto in corso | Indica che è in corso l'arresto della macchina virtuale. | 
| Arrestato | Indica che la macchina virtuale è stata arrestata. Si noti che alle macchine virtuali con stato arrestato continuano a essere addebitati i costi di calcolo.  |
| Deallocazione | Indica che è in corso la deallocazione della macchina virtuale. |
| Deallocato | Indica che la macchina virtuale è stata rimossa completamente dall'hypervisor, ma è ancora disponibile nel piano di controllo. Alle macchine virtuali con stato deallocato non vengono addebitati i costi di calcolo. |
| - | Indica che lo stato di alimentazione della macchina virtuale è sconosciuto. |

### <a name="find-power-state"></a>Trovare lo stato di alimentazione

Per recuperare lo stato di una determinata VM, usare il comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Assicurarsi di specificare un nome valido per una macchina virtuale e un gruppo di risorse. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Output:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È consigliabile creare script per automatizzare le attività ripetitive o complesse. Tramite Azure PowerShell è possibile eseguire molte attività di gestione comuni dalla riga di comando o con script.

### <a name="stop-virtual-machine"></a>Arrestare la macchina virtuale

Arrestare e deallocare una macchina virtuale con [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

Se si vuole mantenere la macchina virtuale in uno stato di provisioning, usare il parametro -StayProvisioned.

### <a name="start-virtual-machine"></a>Avviare la macchina virtuale

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono illustrate la creazione e la gestione di VM di base, ad esempio:

> [!div class="checklist"]
> * Creare e connettersi a una macchina virtuale
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare macchine virtuali di dimensioni specifiche
> * Ridimensionare una VM
> * Visualizzare e comprendere lo stato di una macchina virtuale

Passare all'esercitazione successiva per informazioni sui dischi di macchine virtuali.  

> [!div class="nextstepaction"]
> [Creare e gestire dischi di macchine virtuali](./tutorial-manage-data-disk.md)
