---
title: Esercitazione - Gestire i dischi di Azure con Azure PowerShell | Microsoft Docs
description: In questa esercitazione viene descritto come usare Azure PowerShell per creare e gestire dischi di Azure per macchine virtuali
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
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: 50470edff81194b9c8885aa94d1eab1e6c18ad88
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984087"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Esercitazione: gestire i dischi di Azure con Azure PowerShell

Le macchine virtuali di Azure usano dischi per archiviare sistema operativo, applicazioni e dati di macchine virtuali. Quando si crea una macchina virtuale, è importante scegliere le dimensioni del disco e la configurazione appropriate per il carico di lavoro previsto. Questa esercitazione illustra la distribuzione e la gestione dei dischi di VM. Vengono fornite informazioni su:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegamento e preparazione dei dischi dati

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="default-azure-disks"></a>Dischi di Azure predefiniti

Quando viene creata una macchina virtuale di Azure, due dischi vengono automaticamente collegati alla macchina virtuale. 

**Disco del sistema operativo**: i dischi del sistema operativo possono essere ridimensionati fino a 4 terabyte e ospitano il sistema operativo delle macchine virtuali.  Al disco del sistema operativo viene assegnata una lettera di unità *C:* per impostazione predefinita. La configurazione della memorizzazione nella cache del disco del sistema operativo è ottimizzata per le prestazioni del sistema operativo. Il disco del sistema operativo **non deve** ospitare applicazioni o dati. Per le applicazioni e i dati, usare un disco dati, descritto in dettaglio più avanti in questo articolo.

**Disco temporaneo**: i dischi temporanei usano un'unità SSD che si trova nello stesso host della macchina virtuale di Azure. I dischi temporanei sono altamente efficienti e possono essere usati per operazioni quali l'elaborazione dei dati temporanei. Tuttavia, se la macchina virtuale viene spostata in un nuovo host, tutti i dati memorizzati su un disco temporaneo vengono rimossi. Le dimensioni del disco temporaneo sono determinate dalle [dimensioni della macchina virtuale](sizes.md). Ai dischi temporanei viene assegnata una lettera di unità *D:* per impostazione predefinita.



## <a name="azure-data-disks"></a>Dischi dati di Azure

È possibile aggiungere altri dischi dati per l'installazione di applicazioni e l'archiviazione dei dati. I dischi dati devono essere usati in qualsiasi situazione in cui si richiede un'archiviazione dei dati durevoli e reattiva. Ciascun disco dati ha una capacità massima di 4 terabyte. Le dimensione della macchina virtuale determinano il numero di dischi dati possono essere collegati a una macchina virtuale. Per ogni vCPU della macchina virtuale, è possibile collegare quattro dischi dati. 


## <a name="vm-disk-types"></a>Tipi di dischi per la VM

Azure offre due tipi di dischi.

**Dischi Standard**: sono supportati da unità disco rigido e offrono un'archiviazione conveniente con buone prestazioni. I dischi standard sono ideali per un carico di lavoro di test e sviluppo conveniente.

**Dischi Premium**: sono supportati da un disco a bassa latenza e ad alte prestazioni basato su SSD. Ideale per le macchine virtuali che eseguono il carico di lavoro della produzione. L'archiviazione premium supporta le macchine virtuali serie DS, DSv2, GS e FS. I dischi Premium sono di cinque tipi, P10, P20, P30, P40 e P50. Le dimensioni del disco determinano il tipo di disco. Quando si effettua la selezione, il valore delle dimensioni di un disco viene arrotondato per eccesso al tipo successivo. Ad esempio, se le dimensioni sono inferiori a 128 GB, il tipo di disco è P10 oppure se sono comprese tra 129 GB e 512 GB, il disco è P20.

### <a name="premium-disk-performance"></a>Prestazioni disco premium

|Tipo di disco di Archiviazione Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 | p60 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Dimensioni del disco (arrotondate) | 32 GiB | 64 GiB | 128 GiB | 512 GiB | 1.024 GiB (1 TiB) | 2.048 GiB (2 TiB) | 4.095 GiB (4 TiB) | 8.192 GiB (8 TiB)
| Operazioni IOPS al secondo max per disco | 120 | 240 | 500 | 2.300 | 5.000 | 7.500 | 7.500 | 12.500 |
Velocità effettiva per disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s | 480 MB/s |

Sebbene la tabella sopra riportata identifichi il numero massimo di operazioni di I/O al secondo per disco, è possibile raggiungere un livello superiore di prestazioni tramite lo striping di più dischi di dati. Ad esempio, è possibile collegare 64 dati dischi alla macchina virtuale Standard_GS5. Se ognuno di questi dischi viene ridimensionato come un P30, è possibile ottenere un massimo di 80.000 operazioni di I/O al secondo. Per informazioni dettagliate sul numero massimo di operazioni di I/O al secondo per macchina virtuale, vedere [Tipi e dimensioni delle macchine virtuali](./sizes.md).

## <a name="create-and-attach-disks"></a>Creare e collegare dischi

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, creare una macchina virtuale con i comandi seguenti.

Impostare il nome utente e la password necessari per l'account amministratore della macchina virtuale con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):


Creare la macchina virtuale con [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Verrà richiesto di immettere un nome utente e una password per l'account amministratore per la macchina virtuale.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Creare la configurazione iniziale con [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig). L'esempio seguente configura un disco delle dimensioni di 128 GB.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Creare il disco dati con il comando [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Recuperare la macchina virtuale a cui si vuole aggiungere il disco dati con il comando [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Aggiungere il disco dati alla configurazione della macchina virtuale con il comando [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Aggiornare la macchina virtuale con il comando [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Preparare i dischi di dati

Dopo aver collegato un disco alla macchina virtuale, il sistema operativo deve essere configurato per l'uso del disco. Nell'esempio seguente viene illustrato come configurare manualmente il primo disco aggiunto alla macchina virtuale. Questo processo può essere automatizzato tramite l'[estensione dello script personalizzata](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configurazione manuale

Creare una connessione RDP alla macchina virtuale. Aprire PowerShell ed eseguire questo script.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Verificare il disco dati

Per verificare che il disco dati sia collegato, visualizzare `StorageProfile` per l'oggetto `DataDisks` collegato.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

L'output dovrebbe essere simile all'esempio seguente:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati descritti argomenti relativi ai dischi delle VM, ad esempio:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegamento e preparazione dei dischi dati

Passare all'esercitazione successiva per informazioni sull'automazione della configurazione delle macchine virtuali.

> [!div class="nextstepaction"]
> [Automatizzare la configurazione delle macchine virtuali](./tutorial-automate-vm-deployment.md)
