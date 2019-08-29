---
title: Creare un'immagine gestita in Azure | Microsoft Docs
description: Creare un'immagine gestita di un disco rigido virtuale o una macchina virtuale generalizzati in Azure. È possibile usare le immagini per creare più macchine virtuali che usino i dischi gestiti.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 33f3b03ba76a0c3fd33e057d0f15b2ab7a0f44e4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089491"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Creare un'immagine gestita di una macchina virtuale generalizzata in Azure

È possibile creare una risorsa di tipo immagine gestita da una macchina virtuale (VM) generalizzata che è stata archiviata come disco gestito o come disco non gestito in un account di archiviazione. L'immagine è quindi utilizzabile per creare più macchine virtuali. Per informazioni sulla fatturazione delle immagini gestite, vedere [Prezzi per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove tutte le informazioni sull'account personale e sulla sicurezza e quindi prepara la macchina da usare come immagine. Per informazioni su Sysprep, vedere [Sysprep overview](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) (Panoramica di Sysprep).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [supporto Sysprep per ruoli server](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) e [scenari non supportati](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Dopo aver eseguito Sysprep in una VM, quest'ultima viene considerata *generalizzata* e non può essere riavviata. Il processo di generalizzazione di una macchina virtuale è irreversibile. Per mantenere in funzionamento la VM originale, è consigliabile creare una [copia della VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalizzarla. 
>
> Se si prevede di eseguire Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Per generalizzare la VM Windows, seguire questa procedura:

1. Accedere alla VM Windows.
   
2. Aprire una finestra del Prompt dei comandi come amministratore. Impostare la directory su %windir%\system32\sysprep e quindi eseguire `sysprep.exe`.
   
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e quindi selezionare la casella di controllo **Generalizza**.
   
4. In **Opzioni di arresto** selezionare **Arresta il sistema**.
   
5. Selezionare **OK**.
   
    ![Avvio di Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Al termine, Sysprep arresta la VM. Non riavviare la VM.


## <a name="create-a-managed-image-in-the-portal"></a>Creare un'immagine gestita nel portale 

1. Aprire il [portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra selezionare **Macchine virtuali** e quindi scegliere la VM dall'elenco.

3. Nella pagina **Macchina virtuale** della VM, nel menu superiore, selezionare **Acquisisci**.

   Viene visualizzata la pagina **Crea immagine**.

4. Per il campo **Nome** accettare il nome già popolato oppure immettere un nome che si vuole usare per l'immagine.

5. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome oppure selezionare **Usa esistente** e scegliere un gruppo di risorse da usare dall'elenco a discesa.

6. Per eliminare la VM di origine dopo che l'immagine è stata creata, selezionare **Elimina automaticamente questa macchina virtuale dopo aver creato l'immagine**.

7. Per poter usare l'immagine in qualsiasi [zona di disponibilità](../../availability-zones/az-overview.md), selezionare **Sì** per **Resilienza della zona**.

8. Selezionare **Crea** per creare l'immagine.

9. Dopo averla creata, l'immagine sarà disponibile come risorsa di tipo **Immagine** nell'elenco delle risorse del gruppo.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Creare un'immagine di una macchina virtuale tramite PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

La creazione di un'immagine direttamente nella VM garantisce che l'immagine includa tutti i dischi associati alla VM, compresi il disco del sistema operativo e gli eventuali dischi dati. Questo esempio illustra come creare un'immagine gestita da una VM che usa dischi gestiti.

Prima di iniziare, verificare di avere la versione più recente del modulo Azure PowerShell. Per trovare la versione, eseguire `Get-Module -ListAvailable Az` in PowerShell. Se è necessario eseguire l'aggiornamento, vedere [Install Azure PowerShell on Windows with PowerShellGet](/powershell/azure/install-az-ps) (Installare Azure PowerShell in Windows con PowerShellGet). Se si esegue PowerShell in locale, eseguire `Connect-AzAccount` per creare una connessione con Azure.


> [!NOTE]
> Se si vuole archiviare l'immagine in una risorsa di archiviazione con ridondanza della zona, è necessario crearla in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `-ZoneResilient` nella configurazione dell'immagine (comando `New-AzImageConfig`).

Per creare un'immagine della VM, seguire questa procedura:

1. Creare alcune variabili.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assicurarsi che la macchina virtuale sia stata deallocata.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Impostare lo stato della macchina virtuale su **Generalizzato**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Trovare la macchina virtuale. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Creare un'immagine da un disco gestito usando PowerShell

Se si vuole creare un'immagine del solo disco del sistema operativo, specificare l'ID del disco gestito come disco del sistema operativo:

    
1. Creare alcune variabili. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Ottenere la VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Ottenere l'ID del disco gestito.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Creare un'immagine da uno snapshot usando PowerShell

Per creare un'immagine gestita da uno snapshot di una VM generalizzata, seguire questa procedura:

    
1. Creare alcune variabili. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Trovare lo snapshot.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Creare un'immagine da un disco rigido virtuale in un account di archiviazione

Creare un'immagine gestita da un disco rigido virtuale del sistema operativo generalizzato in un account di archiviazione. È necessario l'URI del disco rigido virtuale nell'account di archiviazione, con il formato seguente: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. In questo esempio il disco rigido virtuale si trova in *mystorageaccount* in un contenitore denominato *vhdcontainer* e il nome file del disco rigido virtuale è *vhdfilename.vhd*.


1.  Creare alcune variabili.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Arrestare o deallocare la VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Contrassegnare la macchina virtuale come generalizzata.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Creare un'immagine tramite il disco rigido virtuale del sistema operativo generalizzato.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Passaggi successivi
- [Creare una macchina virtuale da un'immagine gestita](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)    

