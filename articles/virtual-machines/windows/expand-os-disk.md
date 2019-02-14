---
title: Espandere l'unità del sistema operativo di una macchina virtuale Windows in Azure | Documentazione Microsoft
description: Espandere la dimensione dell'unità del sistema operativo di una macchina virtuale tramite Azure PowerShell nel modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: bd863a8ddd9e2277b628673d2146efd8c458c319
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979497"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Come espandere l'unità del sistema operativo di una macchina virtuale

Quando si crea una nuova macchina virtuale (VM) in un gruppo di risorse distribuendo un'immagine da [Azure Marketplace](https://azure.microsoft.com/marketplace/), l'unità del sistema operativo predefinita è spesso di 127 GB. Per impostazione predefinita. alcune immagini hanno dimensioni minori per il disco del sistema operativo. Anche se è possibile aggiungere dischi dati alla VM (il numero dipende dalla SKU scelta) ed è consigliabile installare applicazioni e carichi di lavoro con uso intensivo della CPU su tali dischi aggiuntivi, spesso i clienti devono espandere l'unità del sistema operativo per supportare determinati scenari, come i seguenti:

- Supporto di applicazioni legacy che installano componenti nell'unità del sistema operativo.
- Migrazione di un computer fisico o di una macchina virtuale locali a un'unità del sistema operativo più grande.


> [!IMPORTANT]
> Il ridimensionamento del disco del sistema operativo di una macchina virtuale di Azure ne determinerà il riavvio.
>
> Dopo l'espansione dei dischi è necessario [espandere il volume all'interno del sistema operativo](#expand-the-volume-within-the-os) per sfruttare i vantaggi del disco più grande.
> 


[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


## <a name="resize-a-managed-disk"></a>Ridimensionare un disco gestito

Aprire la finestra di Powershell ISE o Powershell in modalità amministrativa e seguire questa procedura:

1. Accedere all'account Microsoft Azure in modalità Gestione risorse e selezionare la sottoscrizione come segue:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Impostare il nome del gruppo di risorse e il nome della VM come segue:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Ottenere un riferimento alla VM come segue:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrestare la VM prima di ridimensionare il disco come segue:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Ottenere un riferimento al disco del sistema operativo gestito. Impostare le dimensioni del disco del sistema operativo gestito sul valore desiderato e aggiornare il disco come segue:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito per i dischi del sistema operativo è 2048 GB. È possibile espandere il BLOB del disco rigido virtuale oltre tale dimensione, ma il sistema operativo potrà usare solo i primi 2048 GB di spazio.
   > 
   > 
6. L'aggiornamento della VM potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la VM come segue:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

L'operazione è terminata. Con il protocollo RDP applicato alla VM, aprire Gestione computer (o Gestione disco) ed espandere l'unità usando lo spazio appena allocato.

## <a name="resize-an-unmanaged-disk"></a>Ridimensionare un disco non gestito

Aprire la finestra di Powershell ISE o Powershell in modalità amministrativa e seguire questa procedura:

1. Accedere all'account Microsoft Azure in modalità Gestione risorse e selezionare la sottoscrizione come segue:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Impostare il nome del gruppo di risorse e il nome della VM come segue:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Ottenere un riferimento alla VM come segue:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrestare la VM prima di ridimensionare il disco come segue:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Impostare le dimensioni del disco del sistema operativo non gestito sul valore desiderato e aggiornare la macchina virtuale come segue:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito per i dischi del sistema operativo è 2048 GB. È possibile espandere il BLOB del disco rigido virtuale oltre tale dimensione, ma il sistema operativo potrà usare solo i primi 2048 GB di spazio.
   > 
   > 
   
6. L'aggiornamento della VM potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la VM come segue:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Script per il disco del sistema operativo

Di seguito è riportato per riferimento lo script completo per i dischi gestiti e non gestiti:


**Dischi gestiti**

```Powershell
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



Analogamente è possibile fare riferimento agli altri dischi dati collegati alla macchina virtuale, usando un indice come quello illustrato in precedenza oppure la proprietà **Name** del disco:


**Dischi gestiti**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Dischi non gestiti**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Espandere il volume all'interno del sistema operativo

Dopo aver espanso il disco della macchina virtuale è necessario passare nel sistema operativo ed espanderne il volume per includere il nuovo spazio. Esistono diversi metodi per l'espansione di una partizione. Questa sezione illustra la connessione alla macchina virtuale con una connessione RDP per espandere la partizione utilizzando **DiskPart**.

1. Aprire una connessione RDP alla macchina virtuale.

2.  Aprire il prompt dei comandi e digitare **diskpart**.

2.  Al prompt di **DISKPART** digitare `list volume`. Prendere nota del volume da estendere.

3.  Al prompt di **DISKPART** digitare `select volume <volumenumber>`. Ciò consente di selezionare il volume *volumenumber* che si intende estendere nello spazio vuoto contiguo sullo stesso disco.

4.  Al prompt di **DISKPART** digitare `extend [size=<size>]`. Ciò consente di estende il volume selezionato nelle *dimensioni* in megabyte (MB).


## <a name="next-steps"></a>Passaggi successivi

È consentito allegare dischi anche usando il [portale di Azure](attach-managed-disk-portal.md).
