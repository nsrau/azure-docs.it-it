---
title: Creare una macchina virtuale di Azure gestita da un disco rigido virtuale locale generalizzato | Microsoft Docs
description: Caricare un disco rigido virtuale generalizzato in Azure e usarlo per creare nuove macchine virtuali nel modello di distribuzione Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 93d5cbdb44d3014b547141d59ce96cf607276846
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234613"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Caricare un disco rigido virtuale generalizzato e usarlo per creare nuove macchine virtuali in Azure

Questo articolo illustra come usare PowerShell per caricare un disco rigido virtuale di una macchina virtuale generalizzata in Azure, creare un'immagine dal disco rigido virtuale e quindi una nuova macchina virtuale dall'immagine. È possibile caricare un disco rigido virtuale esportato da uno strumento di virtualizzazione locale o da un altro cloud. Usando [Managed Disks](managed-disks-overview.md) per la nuova macchina virtuale è possibile semplificarne la gestione e ottenere una maggiore disponibilità posizionando la macchina virtuale in un set di disponibilità. 

Per uno script di esempio, vedere [Script di esempio per caricare un disco rigido virtuale in Azure e creare una nuova macchina virtuale](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Prima di iniziare

- Prima di caricare dischi rigidi virtuali in Azure, è necessario seguire la procedura in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Rivedere l'articolo [Plan for the migration to Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) (Piano per la migrazione a Managed Disks) prima di avviare la migrazione a [Managed Disks](managed-disks-overview.md).
- Questo articolo richiede il modulo AzureRM 5.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM.Compute` per trovare la versione in uso. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizzare la macchina virtuale di origine con Sysprep

Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per i dettagli su Sysprep, vedere [Sysprep Overview](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) (Panoramica di Sysprep).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se si prevede di eseguire Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la macchina virtuale](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Accedere alla macchina virtuale Windows.
2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su %windir%\system32\sysprep e quindi eseguire `sysprep.exe`.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
4. In **Opzioni di arresto** selezionare **Arresta il sistema**.
5. Selezionare **OK**.
   
    ![Avvio di Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Al termine, Sysprep arresta la macchina virtuale. Non riavviare la VM.


## <a name="get-a-storage-account"></a>Ottenere un account di archiviazione

Per archiviare l'immagine della macchina virtuale caricata, è necessario un account di archiviazione. È possibile usare un account di archiviazione esistente o crearne uno nuovo. 

Se il disco rigido virtuale verrà usato per creare un disco gestito per una macchina virtuale, la posizione dell'account di archiviazione deve corrispondere al percorso in cui verrà creata la macchina virtuale.

Per visualizzare gli account di archiviazione disponibili, digitare:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Caricare il disco rigido virtuale nell'account di archiviazione

Usare il cmdlet [Add-AzureRmVhd](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd) per caricare il disco rigido virtuale in un contenitore nell'account di archiviazione. In questo esempio, il file *myVHD.vhd* viene caricato da *C:\Users\Public\Documents\Virtual hard disks\\* in un account di archiviazione denominato *mystorageaccount* nel gruppo di risorse *myResourceGroup*. Il file viene inserito nel contenitore denominato *mycontainer* e il nuovo nome del file sarà *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se l'operazione riesce, si ottiene una risposta simile alla seguente:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

L'esecuzione del comando potrebbe richiedere del tempo, a seconda della connessione di rete e delle dimensioni del file VHD.

### <a name="other-options-for-uploading-a-vhd"></a>Altre opzioni per il caricamento di un disco rigido virtuale
 
È anche possibile caricare un disco rigido virtuale nell'account di archiviazione tramite uno dei seguenti modi:

- [AzCopy](https://aka.ms/downloadazcopy)
- [API Copy Blob di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Caricamento di BLOB in Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
- [Materiale di riferimento dell'API REST del servizio di importazione/esportazione dell'archiviazione](https://msdn.microsoft.com/library/dn529096.aspx)
-   È consigliabile usare il servizio di importazione/esportazione se il tempo di caricamento stimato è maggiore di sette giorni. È possibile usare [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) per stimare il tempo in base alla dimensione dei dati e all'unità di trasferimento. 
    Il servizio Importazione/esportazione può essere usato per eseguire la copia in un account di archiviazione standard. È possibile eseguire la copia dall'archiviazione standard all'account di archiviazione Premium usando uno strumento come AzCopy.

> [!IMPORTANT]
> Se per il caricamento del disco rigido virtuale in Azure si usa AzCopy, verificare di aver impostato [**/BlobType:page**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) prima di eseguire lo script di caricamento. Se la destinazione è un BLOB e questa opzione non è specificata, per impostazione predefinita AzCopy crea un BLOB in blocchi.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Creare un'immagine gestita dal disco rigido virtuale caricato 

Creare un'immagine gestita dal disco rigido virtuale del sistema operativo generalizzato. Sostituire i valori seguenti con le informazioni personali.


Per prima cosa, impostare alcuni parametri:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Creare un'immagine tramite il disco rigido virtuale del sistema operativo generalizzato.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Creare la VM

Ora che si dispone di un'immagine, è possibile creare una o più nuove VM dall'immagine. Questo esempio crea una macchina virtuale denominata *myVM* dall'immagine *myImage* in *myResourceGroup*.


```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Passaggi successivi

Accedere alla nuova macchina virtuale. Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

