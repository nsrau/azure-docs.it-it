---
title: Caricare un disco rigido virtuale in Azure o copiare un disco tra le aree geografiche Azure PowerShell
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando Azure PowerShell, tramite il caricamento diretto.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: ec765e2eb635449140d60ee98762e35aea12fe44
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689610"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Caricare un disco rigido virtuale in Azure o copiare un disco gestito in un'altra area: Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare Azure PowerShell modulo](/powershell/azure/install-Az-ps).
- Se si intende caricare un disco rigido virtuale da locale: un disco rigido virtuale di dimensioni fisse [preparato per Azure](prepare-for-upload-vhd-image.md), archiviato localmente.
- In alternativa, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="getting-started"></a>Introduzione

Se si preferisce caricare dischi tramite un'interfaccia utente grafica, è possibile usare Azure Storage Explorer. Per informazioni dettagliate, vedere: [usare Azure Storage Explorer per gestire i dischi gestiti di Azure](../disks-use-storage-explorer-managed-disks.md)

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:

- ReadToUpload, il che significa che il disco è pronto per ricevere un caricamento, ma non è stata generata alcuna [firma di accesso sicuro](../../storage/common/storage-sas-overview.md) (SAS).
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e che la firma di accesso condiviso è stata generata.

> [!NOTE]
> In uno di questi Stati, il disco gestito verrà fatturato in base ai [prezzi standard di HDD](https://azure.microsoft.com/pricing/details/managed-disks/), indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come S10. Questo valore sarà true fino a quando non `revoke-access` viene chiamato sul disco gestito, operazione necessaria per il fissaggio del disco a una macchina virtuale.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuoto

Prima di poter creare un HDD standard vuoto per il caricamento, saranno necessarie le dimensioni del file VHD da caricare, in byte. Il codice di esempio otterrà questa operazione, ma è possibile usare: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Questo valore viene usato quando si specifica il parametro **-UploadSizeInBytes** .

A questo punto, nella shell locale creare un HDD standard vuoto per il caricamento specificando l'impostazione di **caricamento** nel parametro **-CreateOption** e il parametro **-UploadSizeInBytes** nel cmdlet [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) . Quindi chiamare [New-AzDisk](/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) per creare il disco.

Sostituire `<yourdiskname>` , `<yourresourcegroupname>` , `<yourregion>` quindi eseguire i comandi seguenti:

> [!TIP]
> Se si sta creando un disco del sistema operativo, aggiungere-HyperVGeneration ' <yourGeneration> ' a `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Se si vuole caricare un'unità SSD Premium o un'unità SSD standard, sostituire **Standard_LRS** con **Premium_LRS** o **StandardSSD_LRS**. I dischi Ultra non sono ancora supportati.

Ora che è stato creato un disco gestito vuoto configurato per il processo di caricamento, è possibile caricarvi un disco rigido virtuale. Per caricare un disco rigido virtuale sul disco, è necessario disporre di una firma di accesso condiviso scrivibile, in modo che sia possibile farvi riferimento come destinazione per il caricamento.

Per generare una firma di accesso condiviso scrivibile del disco gestito vuoto, sostituire `<yourdiskname>` e `<yourresourcegroupname>` , quindi usare i comandi seguenti:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Caricare il VHD

Ora che si dispone di una firma di accesso condiviso per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy V10 per caricare il file VHD locale in un disco gestito specificando l'URI di firma di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva del [disco rigido standard](disks-types.md#standard-hdd)equivalente. Se, ad esempio, si dispone di una dimensione equivalente a S4, sarà presente una velocità effettiva massima di 60 MiB/s. Tuttavia, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva di un massimo di 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Una volta completato il caricamento e non è più necessario scrivere altri dati sul disco, revocare la firma di accesso condiviso. La revoca della firma di accesso condiviso modificherà lo stato del disco gestito e consentirà di aggiungere il disco a una macchina virtuale.

Sostituire `<yourdiskname>` e `<yourresourcegroupname>` , quindi eseguire il comando seguente:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile eseguire una copia all'interno della stessa area o copiare il disco gestito in un'altra area.

Lo script seguente consente di eseguire questa operazione per l'utente, il processo è simile a quello descritto in precedenza, con alcune differenze, perché si sta lavorando con un disco esistente.

> [!IMPORTANT]
> È necessario aggiungere un offset di 512 quando si specificano le dimensioni del disco in byte di un disco gestito da Azure. Questo perché Azure omette il piè di pagina quando restituisce le dimensioni del disco. Se non si esegue questa operazione, la copia avrà esito negativo. Lo script seguente esegue già questa operazione.

Sostituire `<sourceResourceGroupHere>` , `<sourceDiskNameHere>` , `<targetDiskNameHere>` , `<targetResourceGroupHere>` `<yourOSTypeHere>` e `<yourTargetLocationHere>` (un esempio di un valore di posizione sarebbe uswest2) con i valori, quindi eseguire lo script seguente per copiare un disco gestito.

> [!TIP]
> Se si sta creando un disco del sistema operativo, aggiungere-HyperVGeneration ' <yourGeneration> ' a `New-AzDiskConfig` .

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

Ora che è stato caricato correttamente un disco rigido virtuale in un disco gestito, è possibile collegarlo a una macchina virtuale e iniziare a usarlo.

Per informazioni su come associare un disco dati a una macchina virtuale, vedere l'articolo sull'oggetto: [associare un disco dati a una macchina virtuale Windows con PowerShell](attach-disk-ps.md). Per usare il disco come disco del sistema operativo, vedere [creare una macchina virtuale Windows da un disco specializzato](create-vm-specialized.md#create-the-new-vm).
