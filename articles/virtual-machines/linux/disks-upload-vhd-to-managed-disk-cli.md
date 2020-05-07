---
title: 'Caricare un disco rigido virtuale in Azure o copiare un disco tra aree: interfaccia della riga di comando di Azure'
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando l'interfaccia della riga di comando di Azure, tramite il caricamento diretto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: c32915617d3149eee42bfdfd03d22f9ce5799ef2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580222"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Caricare un disco rigido virtuale in Azure o copiare un disco gestito in un'altra area-interfaccia della riga di comando di Azure

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
- Se si intende caricare un disco rigido virtuale da locale: un disco rigido virtuale di dimensioni fisse [preparato per Azure](../windows/prepare-for-upload-vhd-image.md), archiviato localmente.
- In alternativa, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="getting-started"></a>Introduzione

Se si preferisce caricare dischi tramite un'interfaccia utente grafica, è possibile usare Azure Storage Explorer. Per informazioni dettagliate, vedere: [usare Azure Storage Explorer per gestire i dischi gestiti di Azure](disks-use-storage-explorer-managed-disks.md)

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:

- ReadToUpload, il che significa che il disco è pronto per ricevere un caricamento, ma non è stata generata alcuna [firma di accesso sicuro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e che la firma di accesso condiviso è stata generata.

> [!NOTE]
> In uno di questi Stati, il disco gestito verrà fatturato in base ai [prezzi standard di HDD](https://azure.microsoft.com/pricing/details/managed-disks/), indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come S10. Questo valore sarà true fino `revoke-access` a quando non viene chiamato sul disco gestito, operazione necessaria per il fissaggio del disco a una macchina virtuale.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuoto

Prima di poter creare un HDD standard vuoto per il caricamento, saranno necessarie le dimensioni del file VHD da caricare, in byte. Per ottenere questo valore, è possibile usare `wc -c <yourFileName>.vhd` o `ls -al <yourFileName>.vhd`. Questo valore viene usato quando si specifica il parametro **--upload-size-bytes** .

Creare un HDD standard vuoto per il caricamento specificando il parametro **--for-upload** e il parametro **--upload-size-bytes** in un cmdlet di [creazione disco](/cli/azure/disk#az-disk-create) :

`<yourresourcegroupname>`Sostituire `<yourdiskname>` `<yourregion>` con i valori selezionati. Il `--upload-size-bytes` parametro contiene un valore di esempio `34359738880`di, che deve essere sostituito con un valore appropriato.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se si vuole caricare un'unità SSD Premium o un'unità SSD standard, sostituire **standard_lrs** con **premium_LRS** o **standardssd_lrs**. I dischi Ultra non sono attualmente supportati.

Ora che è stato creato un disco gestito vuoto configurato per il processo di caricamento, è possibile caricarvi un disco rigido virtuale. Per caricare un disco rigido virtuale sul disco, è necessario disporre di una firma di accesso condiviso scrivibile, in modo che sia possibile farvi riferimento come destinazione per il caricamento.

Per generare una firma di accesso condiviso scrivibile del disco gestito vuoto `<yourdiskname>`, `<yourresourcegroupname>`sostituire e, quindi usare il comando seguente:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Valore restituito di esempio:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Caricare il VHD

Ora che si dispone di una firma di accesso condiviso per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy V10 per caricare il file VHD locale in un disco gestito specificando l'URI di firma di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva del [disco rigido standard](disks-types.md#standard-hdd)equivalente. Se, ad esempio, si dispone di una dimensione equivalente a S4, sarà presente una velocità effettiva massima di 60 MiB/s. Tuttavia, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva di un massimo di 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Una volta completato il caricamento e non è più necessario scrivere altri dati sul disco, revocare la firma di accesso condiviso. La revoca della firma di accesso condiviso modificherà lo stato del disco gestito e consentirà di aggiungere il disco a una macchina virtuale.

Sostituire `<yourdiskname>`e `<yourresourcegroupname>`, quindi usare il comando seguente per rendere il disco utilizzabile:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile eseguire la copia all'interno della stessa area o tra più aree (in un'altra area).

Lo script seguente consente di eseguire questa operazione per l'utente, il processo è simile a quello descritto in precedenza, con alcune differenze poiché si sta lavorando con un disco esistente.

> [!IMPORTANT]
> È necessario aggiungere un offset di 512 quando si specificano le dimensioni del disco in byte di un disco gestito da Azure. Questo perché Azure omette il piè di pagina quando restituisce le dimensioni del disco. Se non si esegue questa operazione, la copia avrà esito negativo. Lo script seguente esegue già questa operazione.

`<sourceResourceGroupHere>`Sostituire `<sourceDiskNameHere>`, `<targetDiskNameHere>`,, `<targetResourceGroupHere>`e `<yourTargetLocationHere>` (un esempio di un valore di posizione sarebbe uswest2) con i valori, quindi eseguire lo script seguente per copiare un disco gestito.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato caricato correttamente un disco rigido virtuale in un disco gestito, è possibile aggiungere il disco come [disco dati a una macchina virtuale esistente](add-disk.md) o [connetterlo a una macchina virtuale come disco del sistema operativo](upload-vhd.md#create-the-vm), per creare una nuova macchina virtuale. 

