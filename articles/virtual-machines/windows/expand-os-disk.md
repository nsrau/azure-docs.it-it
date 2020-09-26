---
title: Espandere l'unità del sistema operativo di una macchina virtuale Windows in Azure
description: Espandere le dimensioni dell'unità del sistema operativo di una macchina virtuale utilizzando Azure PowerShell nel modello di distribuzione di Gestione risorse.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: b739bb94911e24002b359aabfa23583ecfc9de85
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336004"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Come espandere l'unità del sistema operativo di una macchina virtuale

Quando si crea una nuova macchina virtuale (VM) in un gruppo di risorse distribuendo un'immagine da [Azure Marketplace](https://azure.microsoft.com/marketplace/), l'unità del sistema operativo predefinita è spesso 127 GB (alcune immagini hanno dimensioni del disco del sistema operativo minori per impostazione predefinita). Anche se è possibile aggiungere dischi dati alla VM (il numero dipende dallo SKU scelto) ed è consigliabile installare applicazioni e carichi di lavoro con utilizzo intensivo della CPU su questi dischi addendum, spesso i clienti devono espandere l'unità del sistema operativo per supportare scenari specifici:

- Per supportare le applicazioni legacy che installano componenti nell'unità del sistema operativo.
- Per eseguire la migrazione di un computer fisico o di una macchina virtuale da locale con un'unità del sistema operativo più grande.

> [!IMPORTANT]
> Il ridimensionamento di un sistema operativo o di un disco dati di una macchina virtuale di Azure richiede la deallocazione della macchina virtuale.
>
> Dopo l'espansione dei dischi è necessario [espandere il volume all'interno del sistema operativo](#expand-the-volume-within-the-os) per sfruttare i vantaggi del disco più grande.
> 

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Ridimensionare un disco gestito nel portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)passare alla macchina virtuale in cui si vuole espandere il disco. Selezionare **Arresta** per deallocare la VM.
2. Quando la macchina virtuale viene arrestata, nel menu a sinistra in **Impostazioni**selezionare **dischi**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Screenshot che mostra l'opzione dischi selezionata nella sezione impostazioni del menu.":::

 
3. In **nome disco**selezionare il disco che si desidera ridimensionare.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Screenshot che mostra il riquadro dischi con un nome di disco selezionato.":::

4. Nel menu a sinistra in **Impostazioni**selezionare **configurazione**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Screenshot che mostra l'opzione di configurazione selezionata nella sezione impostazioni del menu.":::

5. In **dimensioni (GIB)** selezionare le dimensioni del disco desiderate.
   
   > [!WARNING]
   > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito è 2.048 GB per i dischi del sistema operativo. È possibile espandere il BLOB VHD oltre tale dimensione, ma il sistema operativo funziona solo con i primi 2.048 GB di spazio.
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Screenshot che mostra il riquadro di configurazione con le dimensioni del disco selezionate.":::

6. Selezionare **Salva**.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Screenshot che mostra il riquadro di configurazione con il pulsante Salva selezionato.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Ridimensionare un disco gestito usando PowerShell

Aprire la finestra di PowerShell ISE o PowerShell in modalità amministrativa e attenersi alla procedura seguente:

1. Accedere al proprio account di Microsoft Azure in modalità gestione risorse e selezionare la sottoscrizione:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Impostare il nome del gruppo di risorse e il nome della macchina virtuale:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Ottenere un riferimento alla macchina virtuale:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Arrestare la macchina virtuale prima di ridimensionare il disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Ottenere un riferimento al disco del sistema operativo gestito. Impostare la dimensione del disco del sistema operativo gestito sul valore desiderato e aggiornare il disco:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito è 2.048 GB per i dischi del sistema operativo. È possibile espandere il BLOB VHD oltre tale dimensione, ma il sistema operativo funziona solo con i primi 2.048 GB di spazio.
    > 
         
6. L'aggiornamento della macchina virtuale potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la macchina virtuale:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

L'operazione è terminata. Con il protocollo RDP applicato alla VM, aprire Gestione computer (o Gestione disco) ed espandere l'unità usando lo spazio appena allocato.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Ridimensionare un disco non gestito usando PowerShell

Aprire la finestra di PowerShell ISE o PowerShell in modalità amministrativa e attenersi alla procedura seguente:

1. Accedere al proprio account di Microsoft Azure in modalità gestione risorse e selezionare la sottoscrizione:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Impostare il nome del gruppo di risorse e i nomi delle macchine virtuali:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Ottenere un riferimento alla macchina virtuale:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Arrestare la macchina virtuale prima di ridimensionare il disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Impostare la dimensione del disco del sistema operativo non gestito sul valore desiderato e aggiornare la macchina virtuale:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito è 2.048 GB per i dischi del sistema operativo. È possibile espandere il BLOB VHD oltre tale dimensione, ma il sistema operativo sarà in grado di funzionare solo con i primi 2.048 GB di spazio.
    > 
    > 
   
6. L'aggiornamento della macchina virtuale potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la macchina virtuale:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Script per il disco del sistema operativo

Di seguito è riportato per riferimento lo script completo per i dischi gestiti e non gestiti:


**Dischi gestiti**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Dischi non gestiti**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Ridimensionamento dei dischi dati

Sebbene questo articolo si concentri principalmente sull'espansione del disco del sistema operativo della macchina virtuale, lo script può comunque essere usato per l'espansione dei dischi dati collegati alla macchina virtuale. Per espandere ad esempio il primo disco dati collegato alla VM, sostituire l'oggetto `OSDisk` di `StorageProfile` con la matrice `DataDisks` e usare un indice numerico per ottenere un riferimento al primo disco dati collegato, come illustrato di seguito:

**Dischi gestiti**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Dischi non gestiti**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Analogamente, è possibile fare riferimento ad altri dischi dati collegati alla VM, usando un indice come illustrato in precedenza o la proprietà **Name** del disco:


**Dischi gestiti**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Dischi non gestiti**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Espandere il volume all'interno del sistema operativo

Quando si espande il disco per la macchina virtuale, è necessario passare al sistema operativo ed espandere il volume per includere il nuovo spazio. Esistono diversi metodi per l'espansione di una partizione. Questa sezione illustra la connessione alla macchina virtuale con una connessione RDP per espandere la partizione utilizzando **DiskPart**.

1. Aprire una connessione RDP alla macchina virtuale.

2. Aprire il prompt dei comandi e digitare **diskpart**.

3. Al prompt di **DISKPART** digitare `list volume`. Prendere nota del volume da estendere.

4. Al prompt di **DISKPART** digitare `select volume <volumenumber>`. Ciò consente di selezionare il volume *volumenumber* che si intende estendere nello spazio vuoto contiguo sullo stesso disco.

5. Al prompt di **DISKPART** digitare `extend [size=<size>]`. Ciò consente di estende il volume selezionato nelle *dimensioni* in megabyte (MB).


## <a name="next-steps"></a>Passaggi successivi

È consentito allegare dischi anche usando il [portale di Azure](attach-managed-disk-portal.md).
