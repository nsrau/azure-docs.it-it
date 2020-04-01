---
title: Caricare un disco rigido virtuale in Azure o copiare un disco tra aree - Interfaccia della riga di comando di AzureUpload a VHD to Azure or copy a disk across regions - Azure CLI
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando l'interfaccia della riga di comando di Azure tramite il caricamento diretto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420957"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Caricare un disco rigido virtuale in Azure o copiare un disco gestito in un'altra area - Interfaccia della riga di comando di AzureUpload a VHD to Azure or copy a managed disk to another region - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)
- Se si intende caricare un disco rigido virtuale da locale: un disco rigido virtuale a dimensione fissa [preparato per Azure,](../windows/prepare-for-upload-vhd-image.md)archiviato in locale.
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

Prima di poter creare un hdD standard vuoto per il caricamento, è necessario che la dimensione del file del disco rigido virtuale venga caricata in byte. Per ottenere questo, è `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`possibile utilizzare uno o . Questo valore viene utilizzato quando si specifica il parametro **--upload-size-bytes.**

Creare un disco rigido standard vuoto per il caricamento specificando sia il parametro **-–for-upload** che il parametro **--upload-size-bytes** in un cmdlet [disk create:](/cli/azure/disk#az-disk-create)

Sostituire `<yourdiskname>` `<yourresourcegroupname>`, `<yourregion>` , con i valori di propria scelta. Il `--upload-size-bytes` parametro contiene `34359738880`un valore di esempio di , sostituirlo con un valore appropriato.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se si desidera caricare un SSD premium o un SSD standard, sostituire **standard_lrs** con **premium_LRS** o **standardssd_lrs**. Per il momento i dischi ultra non sono supportati.

Dopo aver creato un disco gestito vuoto configurato per il processo di caricamento, è possibile caricarvi un disco rigido virtuale. Per caricare un disco rigido virtuale sul disco, è necessaria una coda di accesso utenti scrivibile, in modo da potervi fare riferimento come destinazione per il caricamento.

Per generare una sAS scrivibile del `<yourdiskname>`disco `<yourresourcegroupname>`gestito vuoto, sostituire e , quindi utilizzare il comando seguente:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Esempio di valore restituito:Sample returned value:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Caricare il VHD

Ora che si dispone di una sAS per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy v10 per caricare il file VHD locale su un disco gestito specificando l'URI di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva [dell'HDD standard](disks-types.md#standard-hdd)equivalente. Ad esempio, se si dispone di una dimensione che equivale a S4, si disputa una velocità effettiva fino a 60 MiB/s. Ma, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva fino a 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Al termine del caricamento e non è più necessario scrivere altri dati sul disco, revocare la server di accesso sAS. La revoca della chiamata a chiamata accesso la chiamata a accesso locale modificherà lo stato del disco gestito e consentirà di collegare il disco a una macchina virtuale.

Sostituire `<yourdiskname>` `<yourresourcegroupname>`e , quindi utilizzare il comando seguente per rendere utilizzabile il disco:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile copiare all'interno della stessa regione o tra aree (in un'altra regione).

Lo script seguente eseguirà questa operazione per te, il processo è simile ai passaggi descritti in precedenza, con alcune differenze poiché stai lavorando con un disco esistente.

> [!IMPORTANT]
> È necessario aggiungere un offset di 512 quando si fornisce la dimensione del disco in byte di un disco gestito da Azure.You need to add an offset of 512 when you're providing the disk size in bytes of a managed disk from Azure. Ciò è dovuto al fatto che Azure omette il piè di pagina quando restituisce le dimensioni del disco. La copia avrà esito negativo se non si esegue questa operazione. Lo script seguente esegue già questa operazione per l'utente.

Sostituire `<sourceResourceGroupHere>`, `<sourceDiskNameHere>` `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourTargetLocationHere>` e (un esempio di valore di posizione sarebbe uswest2) con i valori, quindi eseguire lo script seguente per copiare un disco gestito.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver caricato correttamente un disco rigido virtuale su un disco gestito, è possibile collegare il disco come [disco dati a](add-disk.md) una macchina virtuale esistente o collegare il disco a una macchina virtuale come disco del sistema [operativo](upload-vhd.md#create-the-vm)per creare una nuova macchina virtuale. 

