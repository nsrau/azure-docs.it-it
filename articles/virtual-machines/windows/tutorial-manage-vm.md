---
title: 'Esercitazione: Creare e gestire macchine virtuali Windows con Azure PowerShell | Microsoft Docs'
description: In questa esercitazione viene descritto come usare Azure PowerShell per creare e gestire macchine virtuali Windows in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/02/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b725713777eb6ca25c829d327f91921b28cd4203
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035969"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Esercitazione: Creare e gestire macchine virtuali Windows con Azure PowerShell

Le macchine virtuali di Azure offrono un ambiente di elaborazione completamente configurabile e flessibile. Questa esercitazione illustra le attività di base della distribuzione di una macchina virtuale di Azure, ad esempio la selezione delle dimensioni di una macchina virtuale, la selezione dell'immagine di una macchina virtuale e la distribuzione di una macchina virtuale. Si apprenderà come:

> [!div class="checklist"]
> * Creare e connettersi a una macchina virtuale
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare macchine virtuali di dimensioni specifiche
> * Ridimensionare una VM
> * Visualizzare e comprendere lo stato di una macchina virtuale

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroupVM* nell'area *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Il gruppo di risorse viene specificato quando si crea o si modifica una VM, come viene illustrato in questa esercitazione.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Per la creazione di una macchina virtuale sono disponibili diverse opzioni, ad esempio l'immagine del sistema operativo, la configurazione di rete e le credenziali amministrative. In questo esempio viene creata una macchina virtuale denominata *myVM*, che esegue la versione predefinita di Windows Server 2016 Datacenter.

Impostare il nome utente e la password necessari per l'account amministratore della VM con [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare la macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
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
Get-AzureRmPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Usare il comando seguente nel computer locale per creare una sessione desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo *publicIPAddress* della macchina virtuale. Quando richiesto, immettere le credenziali usate durante la creazione della macchina virtuale.

```powershell
mstsc /v:<publicIpAddress>
```

Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare il nome utente e la password creati per la macchina virtuale e quindi fare clic su **OK**.

## <a name="understand-marketplace-images"></a>Informazioni sulle immagini del marketplace

Azure Marketplace include diverse immagini che possono essere usate per creare una nuova macchina virtuale. Nei passaggi precedenti è stata creata una macchina virtuale usando l'immagine Windows Server 2016 Datacenter. In questo passaggio viene usato il modulo PowerShell per cercare nel marketplace altre immagini di Windows che possono essere usate come base per nuove macchine virtuali. Questo processo consiste nell'individuazione del server di pubblicazione, dell'offerta, dello SKU e, facoltativamente, di un numero di versione per [identificare](cli-ps-findimage.md#terminology) l'immagine.

Usare il comando [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) per restituire un elenco di server di pubblicazione di immagini:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Usare [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) per restituire un elenco di offerte delle immagini. Con questo comando, l'elenco restituito viene filtrato nel server di pubblicazione specificato denominato `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

Il risultato dovrebbe essere analogo all'esempio seguente: 

```powershell
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

Il risultato dovrebbe essere analogo all'esempio seguente: 

```powershell
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

È possibile usare queste informazioni per distribuire una macchina virtuale con un'immagine specifica. Questo esempio distribuisce una macchina virtuale tramite la versione più recente di Windows Server 2016 con un'immagine dei contenitori.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Il parametro `-AsJob` crea la VM come attività in background, in modo che i prompt di PowerShell vengano restituiti all'utente. È possibile visualizzare i dettagli dei processi in background con il cmdlet `Get-Job`.

## <a name="understand-vm-sizes"></a>Informazioni sulle dimensioni delle VM

La dimensioni di una macchina virtuale determinano la quantità di risorse di calcolo, ad esempio CPU, GPU e memoria, disponibili per la macchina virtuale. Le macchine virtuali dovrebbero essere create con dimensioni adeguate per il carico di lavoro. Se un carico di lavoro aumenta, è possibile ridimensionare anche una macchina virtuale esistente.

### <a name="vm-sizes"></a>Dimensioni delle VM

La tabella seguente classifica le dimensioni a seconda dei casi d'uso.  
| type                     | Dimensioni comuni           |    DESCRIZIONE       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Rapporto equilibrato tra CPU e memoria. Soluzione ideale per sviluppo/test e soluzioni di dati e applicazioni medio-piccole.  |
| [Ottimizzate per il calcolo](sizes-compute.md)   | Fsv2, Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per applicazioni con livelli medi di traffico, dispositivi di rete e processi batch.        |
| [Ottimizzate per la memoria](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Rapporto elevato tra memoria e core. Soluzione ideale per database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](sizes-storage.md)      | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | VM specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video.       |
| [Prestazioni elevate](sizes-hpc.md) | H        | Le VM con CPU più potenti, con interfacce di rete ad alta velocità effettiva opzionali (RDMA). |

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
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Se nel cluster corrente non sono disponibili le dimensioni desiderate, è necessario deallocare la macchina virtuale prima di poter eseguire l'operazione di ridimensionamento. La deallocazione di una macchina virtuale rimuoverà tutti i dati sul disco temporaneo e l'indirizzo IP pubblico verrà modificato, a meno che non venga usato un indirizzo IP statico.

```azurepowershell-interactive
Stop-AzureRmVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzureRmVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzureRmVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Stati di alimentazione di una macchina virtuale

Una macchina virtuale di Azure può avere uno dei diversi stati di alimentazione. Questo stato rappresenta lo stato corrente della VM dal punto di vista dell'hypervisor.

### <a name="power-states"></a>Stati di alimentazione

| Stato di alimentazione | DESCRIZIONE
|----|----|
| Avvio in corso | Indica che è in corso l'avvio della macchina virtuale. |
| In esecuzione | Indica che la macchina virtuale è in esecuzione. |
| Stopping | Indica che è in corso l'arresto della macchina virtuale. |
| Arrestato | Indica che la macchina virtuale è stata arrestata. Alle macchine virtuali con stato arrestato continuano a essere addebitati i costi di calcolo.  |
| Deallocazione | Indica che è in corso la deallocazione della macchina virtuale. |
| Deallocato | Indica che la macchina virtuale è stata rimossa dall'hypervisor, ma è ancora disponibile nel piano di controllo. Alle macchine virtuali con stato `Deallocated` non vengono addebitati i costi di calcolo. |
| - | Indica che lo stato di alimentazione della macchina virtuale è sconosciuto. |

### <a name="find-power-state"></a>Trovare lo stato di alimentazione

Per recuperare lo stato di una determinata VM, usare il comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Assicurarsi di specificare un nome valido per una macchina virtuale e un gruppo di risorse.

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Verrà visualizzato un risultato simile all'esempio seguente:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È consigliabile creare script per automatizzare le attività ripetitive o complesse. Tramite Azure PowerShell è possibile eseguire molte attività di gestione comuni dalla riga di comando o con script.

### <a name="stop-a-vm"></a>Arrestare una macchina virtuale

Arrestare e deallocare una macchina virtuale con [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Se si vuole mantenere la macchina virtuale in uno stato di provisioning, usare il parametro -StayProvisioned.

### <a name="start-a-vm"></a>Avviare una macchina virtuale

```azurepowershell-interactive
Start-AzureRmVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute.

```azurepowershell-interactive
Remove-AzureRmResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
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
