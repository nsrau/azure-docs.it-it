---
title: Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell | Microsoft Docs
description: 'Esercitazione: creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4cf406dfbab40631c99da70085e99ba90f563411
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
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

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.3 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroupVM* nell'area *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

Il gruppo di risorse viene specificato quando si crea o si modifica una VM, come viene illustrato in questa esercitazione.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Per la creazione di una macchina virtuale sono disponibili diverse opzioni, ad esempio l'immagine del sistema operativo, configurazione di rete e credenziali amministrative. In questo esempio viene creata una macchina virtuale con un nome *myVM* che esegue la versione predefinita più recente di Windows Server 2016 Datacenter.

Impostare il nome utente e la password necessari per l'account amministratore della macchina virtuale con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare la macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Connettersi alla macchina virtuale

Dopo aver completato la distribuzione, creare una connessione desktop remoto con la macchina virtuale.

Eseguire i comandi seguenti per restituire l'indirizzo IP pubblico della macchina virtuale. Annotare questo indirizzo IP, in modo da potersi connettere ad esso con il browser per testare la connettività Web in un passaggio futuro.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Usare il comando seguente nel computer locale per creare una sessione desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo *publicIPAddress* della macchina virtuale. Quando richiesto, immettere le credenziali utilizzate durante la creazione della macchina virtuale.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Informazioni sulle immagini delle VM

Azure Marketplace include diverse immagini di macchine virtuali che possono essere usate per creare nuove VM. Nei passaggi precedenti è stata creata una macchina virtuale usando un'immagine Windows Server 2016-Datacenter. In questo passaggio, viene usato il modulo PowerShell per cercare nel marketplace altre immagini di Windows da usare anche come base per le nuove macchine virtuali. Questo processo consiste nell'individuazione del server di pubblicazione, dell'offerta e del nome dell'immagine (Sku). 

Usare il comando [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) per restituire un elenco di server di pubblicazione di immagini:

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Usare [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) per restituire un elenco di offerte delle immagini. Con questo comando, l'elenco restituito viene filtrato nel server di pubblicazione specificato:

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

È possibile usare queste informazioni per distribuire una macchina virtuale con un'immagine specifica. Questo esempio distribuisce una macchina virtuale tramite Windows Server 2016 con immagine del contenitore.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Il parametro `-AsJob` crea la VM come attività in background, in modo che i prompt di PowerShell vengano restituiti all'utente. È possibile visualizzare i dettagli dei processi in background con il cmdlet `Job`.


## <a name="understand-vm-sizes"></a>Informazioni sulle dimensioni delle VM

La dimensioni di una macchina virtuale determinano la quantità di risorse di calcolo, ad esempio CPU, GPU e memoria, disponibili per la macchina virtuale. Le macchine virtuali devono essere create con dimensioni adeguate al carico di lavoro previsto. Se aumenta il carico di lavoro, è possibile ridimensionare una macchina virtuale esistente.

### <a name="vm-sizes"></a>Dimensioni delle VM

La tabella seguente classifica le dimensioni a seconda dei casi d'uso.  
| type                     | Dimensioni comuni           |    DESCRIZIONE       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Rapporto equilibrato tra CPU e memoria. Soluzione ideale per sviluppo/test e soluzioni di dati e applicazioni medio-piccole.  |
| [Ottimizzate per il calcolo](sizes-compute.md)   | Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per applicazioni con livelli medi di traffico, dispositivi di rete e processi batch.        |
| [Ottimizzate per la memoria](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Rapporto elevato tra memoria e core. Soluzione ideale per database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](sizes-storage.md)      | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | VM specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video.       |
| [Prestazioni elevate](sizes-hpc.md) | H, A8-11          | Le VM con CPU più potenti, con interfacce di rete ad alta velocità effettiva opzionali (RDMA). 


### <a name="find-available-vm-sizes"></a>Trovare le dimensioni delle macchine virtuali disponibili

Per visualizzare un elenco delle dimensioni delle VM disponibili in una determinata area, usare il comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Ridimensionare una VM

Dopo la distribuzione di una VM, è possibile ridimensionarla per aumentare o ridurre l'allocazione delle risorse.

Prima di ridimensionare una macchina virtuale, verificare che le dimensioni desiderate siano disponibili nel cluster della VM corrente. Il comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) restituisce un elenco di dimensioni. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Se le dimensioni desiderate sono disponibili, la VM può essere ridimensionata mentre è accesa, ma durante l'operazione viene riavviata.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Se nel cluster corrente non sono disponibili le dimensioni desiderate, è necessario deallocare la VM prima di poter eseguire l'operazione di ridimensionamento. Si noti che, quando la macchina virtuale viene riaccesa, vengono rimossi tutti i dati sul disco temporaneo, mentre l'indirizzo IP pubblico cambia a meno che non venga usato un indirizzo IP statico. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>Stati di alimentazione di una macchina virtuale

Una macchina virtuale di Azure può avere uno dei diversi stati di alimentazione. Questo stato rappresenta lo stato corrente della VM dal punto di vista dell'hypervisor. 

### <a name="power-states"></a>Stati di alimentazione

| Stato di alimentazione | DESCRIZIONE
|----|----|
| Avvio in corso | Indica che è in corso l'avvio della macchina virtuale. |
| In esecuzione | Indica che la macchina virtuale è in esecuzione. |
| Stopping | Indica che è in corso l'arresto della macchina virtuale. | 
| Arrestato | Indica che la macchina virtuale è stata arrestata. Si noti che alle macchine virtuali con stato arrestato continuano a essere addebitati i costi di calcolo.  |
| Deallocazione | Indica che è in corso la deallocazione della macchina virtuale. |
| Deallocato | Indica che la macchina virtuale è stata rimossa completamente dall'hypervisor, ma è ancora disponibile nel piano di controllo. Alle macchine virtuali con stato deallocato non vengono addebitati i costi di calcolo. |
| - | Indica che lo stato di alimentazione della macchina virtuale è sconosciuto. |

### <a name="find-power-state"></a>Trovare lo stato di alimentazione

Per recuperare lo stato di una determinata VM, usare il comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Assicurarsi di specificare un nome valido per una macchina virtuale e un gruppo di risorse. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
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
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

Se si vuole mantenere la macchina virtuale in uno stato di provisioning, usare il parametro -StayProvisioned.

### <a name="start-virtual-machine"></a>Avviare la macchina virtuale

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
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
