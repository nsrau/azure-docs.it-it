---
title: Caricare un disco rigido virtuale in Azure o copiare un disco tra aree - Azure PowerShellUpload a VHD to Azure or copy a disk across regions - Azure PowerShell
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando Azure PowerShell tramite il caricamento diretto.
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 55606aeeb9f6445027f5da49821dbc4970764ade
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421054"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Caricare un disco rigido virtuale in Azure o copiare un disco gestito in un'altra area - Azure PowerShellUpload a VHD to Azure or copy a managed disk to another region - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare il modulo di Azure PowerShell](/powershell/azure/install-Az-ps).
- Se si intende caricare un disco rigido virtuale da locale: un disco rigido virtuale a dimensione fissa [preparato per Azure,](prepare-for-upload-vhd-image.md)archiviato in locale.
- In alternativa, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="getting-started"></a>Introduzione

Se si preferisce caricare dischi tramite una GUI, è possibile farlo usando Azure Storage Explorer.If you'd prefer to upload disks through a GUI, you can do so using Azure Storage Explorer. Per informazioni dettagliate, vedere: [Usare Azure Storage Explorer per gestire i dischi gestiti](disks-use-storage-explorer-managed-disks.md) di AzureFor details refer to: Use Azure Storage Explorer to manage Azure managed disks

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere alcune informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:This kind of managed disk has two unique states:

- ReadToUpload, che significa che il disco è pronto per ricevere un caricamento ma non è stata generata alcuna firma di [accesso sicuro.](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e la sAS è stata generata.

> [!NOTE]
> In uno di questi stati, il disco gestito verrà fatturato al [prezzo dell'HDD standard,](https://azure.microsoft.com/pricing/details/managed-disks/)indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come Un S10. Questo valore sarà `revoke-access` vero fino a quando non verrà chiamato sul disco gestito, che è necessario per collegare il disco a una macchina virtuale.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuotoCreate an empty managed disk

Prima di poter creare un hdD standard vuoto per il caricamento, è necessario che la dimensione del file del disco rigido virtuale venga caricata in byte. Il codice di esempio otterrà che per voi, `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`ma, per farlo da soli è possibile utilizzare: . Questo valore viene utilizzato quando si specifica il parametro **-UploadSizeInBytes.**

A questo punto, nella shell locale, creare un disco rigido standard vuoto per il caricamento specificando l'impostazione **Upload** nel parametro **-CreateOption** e il parametro **-UploadSizeInBytes** nel cmdlet [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Chiamare [quindi New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) per creare il disco.

Sostituire `<yourdiskname>` `<yourresourcegroupname>`, `<yourregion>` , quindi eseguire i seguenti comandi:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Se si desidera caricare un SSD premium o un SSD standard, sostituire **Standard_LRS** con **Premium_LRS** o **StandardSSD_LRS**. I dischi ultra non sono ancora supportati.

Dopo aver creato un disco gestito vuoto configurato per il processo di caricamento, è possibile caricarvi un disco rigido virtuale. Per caricare un disco rigido virtuale sul disco, è necessaria una coda di accesso utenti scrivibile, in modo da potervi fare riferimento come destinazione per il caricamento.

Per generare una sAS scrivibile del `<yourdiskname>`disco `<yourresourcegroupname>`gestito vuoto, sostituire e , quindi utilizzare i comandi seguenti:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Caricare il VHD

Ora che si dispone di una sAS per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy v10 per caricare il file VHD locale su un disco gestito specificando l'URI di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva [dell'HDD standard](disks-types.md#standard-hdd)equivalente. Ad esempio, se si dispone di una dimensione che equivale a S4, si disputa una velocità effettiva fino a 60 MiB/s. Ma, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva fino a 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Al termine del caricamento e non è più necessario scrivere altri dati sul disco, revocare la server di accesso sAS. La revoca della chiamata a chiamata accesso la chiamata a accesso locale modificherà lo stato del disco gestito e consentirà di collegare il disco a una macchina virtuale.

Sostituire `<yourdiskname>` `<yourresourcegroupname>`e , quindi eseguire il comando seguente:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile copiare all'interno della stessa area o copiare il disco gestito in un'altra area.

Lo script seguente eseguirà questa operazione per te, il processo è simile ai passaggi descritti in precedenza, con alcune differenze, dal momento che stai lavorando con un disco esistente.

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
