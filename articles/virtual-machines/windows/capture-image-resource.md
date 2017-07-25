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
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: e428b755f6696bd6d4047ad77579a8e9665dfbd8
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Creare un'immagine gestita di una macchina virtuale generalizzata in Azure

È possibile creare una risorsa immagine gestita da una macchina virtuale generalizzata archiviata come disco gestito o come disco non gestito in un account di archiviazione. L'immagine è quindi utilizzabile per creare più macchine virtuali. 


## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
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


