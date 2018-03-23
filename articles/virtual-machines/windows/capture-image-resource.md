---
title: Creare un'immagine gestita in Azure | Microsoft Docs
description: Creare un'immagine gestita di un disco rigido virtuale o una macchina virtuale generalizzati in Azure. È possibile usare le immagini per creare più macchine virtuali che usino i dischi gestiti.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: cynthn
ms.openlocfilehash: f18defbec79638cf2c3363e6429ddcac01b1502e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Creare un'immagine gestita di una macchina virtuale generalizzata in Azure

È possibile creare una risorsa immagine gestita da una macchina virtuale generalizzata archiviata come disco gestito o come disco non gestito in un account di archiviazione. L'immagine è quindi utilizzabile per creare più macchine virtuali. 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Dopo aver eseguito sysprep in una macchina virtuale, quest'ultima viene considerata *generalizzata* e non può essere riavviata. Il processo di generalizzazione di una macchina virtuale è irreversibile. Per mantenere in funzionamento la macchina virtuale originale, è consigliabile eseguire una [copia della macchina virtuale](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalizzare la copia. 
>
> Se si esegue Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) prima di eseguire Sysprep.  
> 
> 

1. Accedere alla macchina virtuale Windows.
2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\system32\sysprep**, quindi eseguire `sysprep.exe`.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
4. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
5. Fare clic su **OK**.
   
    ![Avvio di Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Al termine, Sysprep arresta la macchina virtuale. Non riavviare la VM.


## <a name="create-a-managed-image-in-the-portal"></a>Creare un'immagine gestita nel portale 

1. Aprire il [portale](https://portal.azure.com).
2. Nel menu a sinistra fare clic su Macchine virtuali e quindi selezionare una VM dall'elenco.
3. Nella pagina della VM scegliere **Acquisisci** dal menu in alto.
3. In **Nome** digitare il nome che si vuole usare per l'immagine.
4. In **Gruppo di risorse** selezionare **Crea nuovo** e digitare un nome oppure scegliere **Usa esistente** e selezionare un gruppo di risorse da usare dall'elenco a discesa.
5. Per eliminare la VM di origine dopo che l'immagine è stata creata, selezionare **Elimina automaticamente questa macchina virtuale dopo aver creato l'immagine**.
6. Al termine dell'operazione fare clic su **Crea**.
16. Dopo avere creato l'immagine, questa verrà visualizzata come risorsa **immagine** nell'elenco delle risorse nel gruppo di risorse.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Creare un'immagine di una macchina virtuale tramite PowerShell

La creazione di un'immagine direttamente dalla macchina virtuale garantisce che l'immagine includa tutti i dischi associati alla macchina virtuale, tra cui il disco del sistema operativo ed eventuali dischi dati. Questo esempio illustra come creare un'immagine gestita da una VM che usa dischi gestiti.


Prima di iniziare, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo. Usare `Get-Module` per determinare la versione in uso.

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](/powershell/azure/overview).


1. Creare alcune variabili.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assicurarsi che la macchina virtuale sia stata deallocata.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Impostare lo stato della macchina virtuale su **Generalizzato**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Trovare la macchina virtuale. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Creare un'immagine da un disco gestito usando PowerShell

Se si vuole solo creare un'immagine del disco del sistema operativo, è anche possibile creare un'immagine specificando l'ID del disco gestito come disco del sistema operativo.

    
1. Creare alcune variabili. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Ottenere la VM.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Ottenere l'ID del disco gestito.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Creare un'immagine da uno snapshot usando PowerShell

È possibile creare un'immagine gestita da uno snapshot di una macchina virtuale generalizzata.

    
1. Creare alcune variabili. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Trovare lo snapshot.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Creare un'immagine da un disco rigido virtuale in un account di archiviazione

Creare un'immagine gestita da un disco rigido virtuale del sistema operativo generalizzato in un account di archiviazione. È necessario l'URI del disco rigido virtuale nell'account di archiviazione, che presenta il formato https://*mystorageaccount*.blob.core.windows.net/*contenitore*/*nomefile_vhd.vhd*. In questo esempio il disco rigido virtuale usato è in *mystorageaccount* in un contenitore denominato *vhdcontainer* e il nome file del disco rigido virtuale è *osdisk.vhd*.


1.  Innanzitutto, impostare i parametri comuni:

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Arrestare/deallocare la macchina virtuale.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Contrassegnare la macchina virtuale come generalizzata.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Creare un'immagine tramite il disco rigido virtuale del sistema operativo generalizzato.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Passaggi successivi
- È ora possibile [creare una macchina virtuale dall'immagine gestita generalizzata](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

