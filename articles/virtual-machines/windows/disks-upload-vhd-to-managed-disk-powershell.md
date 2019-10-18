---
title: Caricare un disco rigido virtuale in Azure usando Azure PowerShell
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando Azure PowerShell.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: d193dcd0c0539c2daa7220d915fdc3e02c8ea798
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512432"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Caricare un disco rigido virtuale in Azure usando Azure PowerShell

Questo articolo illustra come caricare un disco rigido virtuale dal computer locale a un disco gestito di Azure. In precedenza era necessario seguire un processo più impegnativo che includeva la gestione temporanea dei dati in un account di archiviazione e la gestione dell'account di archiviazione. A questo punto, non è più necessario gestire un account di archiviazione o organizzare i dati per il caricamento di un disco rigido virtuale. Viene invece creato un disco gestito vuoto e viene caricato direttamente un disco rigido virtuale. Questo semplifica il caricamento di macchine virtuali locali in Azure e consente di caricare un disco rigido virtuale fino a 32 TiB direttamente in un disco gestito di grandi dimensioni.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, si consiglia di usare il caricamento diretto per ripristinare i backup dei clienti a Managed Disks. Se si sta caricando un disco rigido virtuale da un computer esterno ad Azure, la velocità con dipende dalla larghezza di banda locale. Se si usa una macchina virtuale di Azure, la larghezza di banda sarà identica a quella degli HDD standard.

Attualmente, il caricamento diretto è supportato per HDD standard, unità SSD standard e Managed disks SSD Premium. Non è ancora supportata per le unità SSD ultra.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare Azure PowerShell modulo](/powershell/azure/install-Az-ps).
- Se si intende caricare un disco rigido virtuale da un disco rigido virtuale che [è stato preparato per Azure](prepare-for-upload-vhd-image.md), archiviato localmente.
- In alternativa, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuoto

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:

- ReadToUpload, il che significa che il disco è pronto per ricevere un caricamento, ma non è stata generata alcuna [firma di accesso sicuro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e che la firma di accesso condiviso è stata generata.

In uno di questi Stati, il disco gestito verrà fatturato in base ai [prezzi standard di HDD](https://azure.microsoft.com/pricing/details/managed-disks/), indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come S10. Questo valore sarà true fino a quando non viene chiamato `revoke-access` sul disco gestito, che è necessario per il fissaggio del disco a una macchina virtuale.

Prima di creare un HDD standard vuoto per il caricamento, saranno necessarie le dimensioni del file in byte del disco rigido virtuale che si vuole caricare. Il codice di esempio otterrà questa operazione, ma è possibile usare: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Questo valore viene usato quando si specifica il parametro **-UploadSizeInBytes** .

A questo punto, nella shell locale creare un HDD standard vuoto per il caricamento specificando l'impostazione di **caricamento** nel parametro **-CreateOption** e il parametro **-UploadSizeInBytes** nel cmdlet [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) . Quindi chiamare [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) per creare il disco:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Se si vuole caricare un'unità SSD Premium o un'unità SSD standard, sostituire **Standard_LRS** con **Premium_LRS** o **StandardSSD_LRS**. Ultra SSD non è ancora supportata.

A questo punto è stato creato un disco gestito vuoto configurato per il processo di caricamento. Per caricare un disco rigido virtuale sul disco, è necessario disporre di una firma di accesso condiviso scrivibile, in modo che sia possibile farvi riferimento come destinazione per il caricamento.

Per generare una firma di accesso condiviso scrivibile del disco gestito vuoto, usare il comando seguente:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Carica VHD

Ora che si dispone di una firma di accesso condiviso per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy V10 per caricare il file VHD locale in un disco gestito specificando l'URI di firma di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva del [disco rigido standard](disks-types.md#standard-hdd)equivalente. Se, ad esempio, si dispone di una dimensione equivalente a S4, sarà presente una velocità effettiva massima di 60 MiB/s. Tuttavia, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva di un massimo di 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Se la firma di accesso condiviso scade durante il caricamento e non è ancora stato chiamato `revoke-access`, è possibile ottenere una nuova firma di accesso condiviso per continuare il caricamento usando `grant-access`.

Una volta completato il caricamento e non è più necessario scrivere altri dati sul disco, revocare la firma di accesso condiviso. La revoca della firma di accesso condiviso modificherà lo stato del disco gestito e consentirà di aggiungere il disco a una macchina virtuale.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile eseguire la copia all'interno della stessa area o tra più aree (in un'altra area).

Lo script seguente consente di eseguire questa operazione per l'utente, il processo è simile a quello descritto in precedenza, con alcune differenze poiché si sta lavorando con un disco esistente.

> [!IMPORTANT]
> È necessario aggiungere un offset di 512 quando si specificano le dimensioni del disco in byte di un disco gestito da Azure. Questo perché Azure omette il piè di pagina quando restituisce le dimensioni del disco. Se non si esegue questa operazione, la copia avrà esito negativo. Lo script seguente esegue già questa operazione.

Sostituire il `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` e `<yourTargetLocationHere>` (un esempio di un valore location è uswest2) con i valori, quindi eseguire lo script seguente per copiare un disco gestito.

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
