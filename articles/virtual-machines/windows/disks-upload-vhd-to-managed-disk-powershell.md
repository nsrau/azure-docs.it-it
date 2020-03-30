---
title: Caricare un disco rigido virtuale in Azure usando Azure PowerShellUpload a vhd to Azure using Azure PowerShell
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando Azure PowerShell tramite il caricamento diretto.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369557"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Caricare un disco rigido virtuale in Azure usando Azure PowerShellUpload a vhd to Azure using Azure PowerShell

Questo articolo illustra come caricare un disco rigido dal computer locale a un disco gestito di Azure.This article explains how to upload a vhd from your local machine to an Azure managed disk. In precedenza, era necessario seguire un processo più complesso che includeva la gestione temporanea dei dati in un account di archiviazione e la gestione di tale account di archiviazione. A questo punto, non è più necessario gestire un account di archiviazione o i dati della fase in esso contenuti per caricare un disco rigido virtuale. Si crea invece un disco gestito vuoto e vi si carica direttamente un disco rigido virtuale. Ciò semplifica il caricamento delle macchine virtuali locali in Azure e consente di caricare un disco rigido fino a 32 TiB direttamente in un disco gestito di grandi dimensioni.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, è consigliabile usare il caricamento diretto per ripristinare i backup dei clienti nei dischi gestiti. Se si carica un disco rigido virtuale da un computer esterno ad Azure, le velocità dipendono dalla larghezza di banda locale. Se si usa una macchina virtuale di Azure, la larghezza di banda sarà la stessa degli HDD standard.

Attualmente, il caricamento diretto è supportato per i dischi gestiti HDD standard, SSD standard e SSD premium. Non è ancora supportato per gli SSD ultra.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare il modulo di Azure PowerShell](/powershell/azure/install-Az-ps).
- Se si intende caricare un disco rigido virtuale da locale: un disco rigido virtuale a dimensione fissa [preparato per Azure,](prepare-for-upload-vhd-image.md)archiviato in locale.
- In alternativa, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuotoCreate an empty managed disk

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere alcune informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:This kind of managed disk has two unique states:

- ReadToUpload, che significa che il disco è pronto per ricevere un caricamento ma non è stata generata alcuna firma di [accesso sicuro.](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e la sAS è stata generata.

In uno di questi stati, il disco gestito verrà fatturato al [prezzo dell'HDD standard,](https://azure.microsoft.com/pricing/details/managed-disks/)indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come Un S10. Questo valore sarà `revoke-access` vero fino a quando non verrà chiamato sul disco gestito, che è necessario per collegare il disco a una macchina virtuale.

Prima di creare un hdD standard vuoto per il caricamento, è necessario che la dimensione del file sia in byte del disco rigido virtuale che si desidera caricare. Il codice di esempio otterrà che per voi, `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`ma, per farlo da soli è possibile utilizzare: . Questo valore viene utilizzato quando si specifica il parametro **-UploadSizeInBytes.**

A questo punto, nella shell locale, creare un disco rigido standard vuoto per il caricamento specificando l'impostazione **Upload** nel parametro **-CreateOption** e il parametro **-UploadSizeInBytes** nel cmdlet [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Chiamare [quindi New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) per creare il disco:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Se si desidera caricare un SSD premium o un SSD standard, sostituire **Standard_LRS** con **Premium_LRS** o **StandardSSD_LRS**. Ultra SSD non è ancora supportato.

È stato creato un disco gestito vuoto configurato per il processo di caricamento. Per caricare un disco rigido virtuale sul disco, è necessaria una sAS scrivibile, in modo da potervi fare riferimento come destinazione per il caricamento.

Per generare una sAS scrivibile del disco gestito vuoto, utilizzare il comando seguente:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Caricare vhd

Ora che si dispone di una sAS per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy v10 per caricare il file VHD locale su un disco gestito specificando l'URI di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva [dell'HDD standard](disks-types.md#standard-hdd)equivalente. Ad esempio, se si dispone di una dimensione che equivale a S4, si disputa una velocità effettiva fino a 60 MiB/s. Ma, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva fino a 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Al termine del caricamento e non è più necessario scrivere altri dati sul disco, revocare la server di accesso sAS. La revoca della chiamata a chiamata accesso la chiamata a accesso locale modificherà lo stato del disco gestito e consentirà di collegare il disco a una macchina virtuale.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile copiare all'interno della stessa regione o tra aree (in un'altra regione).

Lo script seguente eseguirà questa operazione per te, il processo è simile ai passaggi descritti in precedenza, con alcune differenze poiché stai lavorando con un disco esistente.

> [!IMPORTANT]
> È necessario aggiungere un offset di 512 quando si fornisce la dimensione del disco in byte di un disco gestito da Azure.You need to add an offset of 512 when you're providing the disk size in bytes of a managed disk from Azure. Ciò è dovuto al fatto che Azure omette il piè di pagina quando restituisce le dimensioni del disco. La copia avrà esito negativo se non si esegue questa operazione. Lo script seguente esegue già questa operazione per l'utente.

Sostituire `<sourceResourceGroupHere>`, `<sourceDiskNameHere>` `<targetDiskNameHere>`, `<targetResourceGroupHere>` `<yourOSTypeHere>` , `<yourTargetLocationHere>` e (un esempio di valore di posizione sarebbe uswest2) con i valori, quindi eseguire lo script seguente per copiare un disco gestito.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver caricato correttamente un disco rigido virtuale su un disco gestito, è possibile collegare il disco a una macchina virtuale e iniziare a usarlo.

Per informazioni su come collegare un disco dati a una macchina virtuale, vedere l'articolo sull'oggetto: [Collegare un disco dati a una macchina virtuale Windows con PowerShell.](attach-disk-ps.md) Per usare il disco come disco del sistema operativo, vedere [Creare una macchina virtuale Windows da un disco specializzato.](create-vm-specialized.md#create-the-new-vm)
