---
title: Caricare un disco rigido virtuale in Azure usando Azure PowerShell
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure usando Azure PowerShell.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 5b7c612d349c3f596487db4af025e5e599b6589c
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694789"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Caricare un disco rigido virtuale in Azure usando Azure PowerShell

Questo articolo illustra come caricare un disco rigido virtuale dal computer locale a un disco gestito di Azure. In precedenza era necessario seguire un processo più impegnativo che includeva la gestione temporanea dei dati in un account di archiviazione e la gestione dell'account di archiviazione. A questo punto, non è più necessario gestire un account di archiviazione o organizzare i dati per il caricamento di un disco rigido virtuale. Viene invece creato un disco gestito vuoto e viene caricato direttamente un disco rigido virtuale. Questo semplifica il caricamento di macchine virtuali locali in Azure e consente di caricare un disco rigido virtuale fino a 32 TiB direttamente in un disco gestito di grandi dimensioni.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, si consiglia di usare il caricamento diretto per ripristinare i backup dei clienti a Managed Disks. Se si sta caricando un disco rigido virtuale da un computer esterno ad Azure, la velocità con dipende dalla larghezza di banda locale. Se si usa una macchina virtuale di Azure, la larghezza di banda sarà identica a quella degli HDD standard.

Attualmente, il caricamento diretto è supportato per HDD standard, unità SSD standard e Managed disks SSD Premium. Non è ancora supportata per le unità SSD ultra.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare Azure PowerShell modulo](/powershell/azure/install-Az-ps).
- Un file VHD, archiviato localmente.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuoto

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:

- ReadToUpload, il che significa che il disco è pronto per ricevere un caricamento, ma non è stata generata alcuna [firma di accesso sicuro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e che la firma di accesso condiviso è stata generata.

In uno di questi Stati, il disco gestito verrà fatturato in base ai [prezzi standard di HDD](https://azure.microsoft.com/pricing/details/managed-disks/), indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come S10. Questo valore sarà true fino `revoke-access` a quando non viene chiamato sul disco gestito, operazione necessaria per il fissaggio del disco a una macchina virtuale.

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
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas --blob-type PageBlob
```

Se la firma di accesso condiviso scade durante il caricamento e non è `revoke-access` ancora stata chiamata, è possibile ottenere una nuova firma di accesso condiviso `grant-access`per continuare il caricamento usando, di nuovo.

Una volta completato il caricamento e non è più necessario scrivere altri dati sul disco, revocare la firma di accesso condiviso. La revoca della firma di accesso condiviso modificherà lo stato del disco gestito e consentirà di aggiungere il disco a una macchina virtuale.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato caricato correttamente un disco rigido virtuale in un disco gestito, è possibile collegarlo a una macchina virtuale e iniziare a usarlo.

Per informazioni su come aggiungere un disco a una macchina virtuale, vedere l'articolo sull'argomento: [Associare un disco dati a una macchina virtuale Windows con PowerShell](attach-disk-ps.md).
