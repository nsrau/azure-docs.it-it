---
title: Creare un&quot;immagine gestita in Azure | Microsoft Docs
description: "Creare un&quot;immagine gestita di un disco rigido virtuale o una macchina virtuale generalizzati in Azure. È possibile usare le immagini per creare più macchine virtuali che usino i dischi gestiti."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c3f5a66605b1a7059a7820ddda9463cb4277f055
ms.lasthandoff: 04/27/2017


---
# <a name="capture-a-managed-image-of-a-generalized-vm-in-azure"></a>Acquisire un'immagine gestita di una macchina virtuale generalizzata in Azure

È possibile creare una risorsa immagine gestita da una macchina virtuale generalizzata che viene archiviata come disco gestito o non gestito in un account di archiviazione. È quindi possibile usare questa immagine per creare più macchine virtuali che usino i dischi gestiti per l'archiviazione. 


## <a name="prerequisites"></a>Prerequisiti
È necessario aver già [ generalizzato la macchina virtuale](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e averla arrestata/deallocata. Generalizzando una VM si rimuovono anche tutte le informazioni personali sull'account e si prepara la macchina da usare come immagine.



## <a name="create-a-managed-image-in-the-portal"></a>Creare un'immagine gestita nel portale 

1. Aprire il [portale](https://portal.azure.com).
2. Fare clic sul segno più per creare una nuova risorsa.
3. Nella ricerca per filtro digitare **Immagine**.
4. Selezionare **Immagine** dai risultati.
5. Nel pannello **Immagine** fare clic su **Crea**.
6. In **Nome** digitare un nome per l'immagine.
7. Se sono disponibili più sottoscrizioni, selezionare quella corretta dall'elenco a discesa **Sottoscrizione**.
7. In **Gruppo di risorse** selezionare **Crea nuovo** e digitare un nome oppure scegliere **From existing** (Da esistente) e selezionare un gruppo di risorse da usare dall'elenco a discesa.
8. In **Percorso** scegliere il percorso del gruppo di risorse.
9. In **Tipo di sistema operativo** selezionare il tipo di sistema operativo, ovvero Windows o Linux.
11. In **BLOB di archiviazione** fare clic su **Sfoglia** per cercare il disco rigido virtuale nell'archiviazione di Azure.
12. In **Tipo di account** scegliere Standard_LRS o Premium_LRS. Il tipo Standard usa unità disco rigido, mente l'account Premium usa unità SSD. Entrambi usano l'archiviazione con ridondanza locale.
13. In **Disk caching** (Memorizzazione nella cache del disco) selezionare l'opzione di memorizzazione nella cache del disco appropriata. Le opzioni sono **Nessuna**, **Di sola lettura** e **Lettura/Scrittura**.
14. Facoltativo: è possibile aggiungere anche un disco dati esistente all'immagine facendo clic su **+ Aggiungi disco dati**.  
15. Dopo aver completato le selezioni, fare clic su **Crea**.
16. Dopo aver creato l'immagine, questa verrà visualizzata come una risorsa **immagine** nell'elenco delle risorse nel gruppo di risorse scelto.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Creare un'immagine gestita di una macchina virtuale tramite PowerShell

La creazione di un'immagine direttamente dalla macchina virtuale garantisce che l'immagine includa tutti i dischi associati alla macchina virtuale, tra cui il disco del sistema operativo ed eventuali dischi dati.


Prima di iniziare, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Azure PowerShell Versioning](/powershell/azure/overview) (Controllo delle versioni di Azure PowerShell).


1. Creare alcune variabili. 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assicurarsi che la macchina virtuale sia stata deallocata.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Impostare lo stato della macchina virtuale su **Generalizzato**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Trovare la macchina virtuale. 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Creare la configurazione dell'immagine.

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Creare l'immagine.

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ```    



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>Creare un'immagine gestita di un disco rigido virtuale tramite PowerShell

Creare un'immagine gestita tramite il disco rigido virtuale del sistema operativo generalizzato.


1.  Innanzitutto, impostare i parametri comuni:

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Arrestare/deallocare la macchina virtuale.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Contrassegnare la macchina virtuale come generalizzata.

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized    
    ```
4.  Creare un'immagine tramite il disco rigido virtuale del sistema operativo generalizzato.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>Creare un'immagine gestita da uno snapshot tramite PowerShell

È inoltre possibile creare un'immagine gestita da uno snapshot del disco rigido virtuale da una macchina virtuale generalizzata.

    
1. Creare alcune variabili. 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Trovare lo snapshot.

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Creare la configurazione dell'immagine.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Creare l'immagine.

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```    
    

## <a name="next-steps"></a>Passaggi successivi
- È ora possibile [creare una macchina virtuale dall'immagine gestita generalizzata](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    


