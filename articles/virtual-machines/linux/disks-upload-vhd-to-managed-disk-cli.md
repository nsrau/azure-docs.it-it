---
title: Caricare un VHD in Azure usando l'interfaccia della riga di comando di Azure
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra le aree usando l'interfaccia della riga di comando di Azure.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: d16e37849ce8ba043fdb1fddb13df2abe8732cda
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71717181"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Caricare un VHD in Azure usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come caricare un disco rigido virtuale dal computer locale a un disco gestito di Azure. In precedenza era necessario seguire un processo più impegnativo che includeva la gestione temporanea dei dati in un account di archiviazione e la gestione dell'account di archiviazione. A questo punto, non è più necessario gestire un account di archiviazione o organizzare i dati per il caricamento di un disco rigido virtuale. Viene invece creato un disco gestito vuoto e viene caricato direttamente un disco rigido virtuale. Questo semplifica il caricamento di macchine virtuali locali in Azure e consente di caricare un disco rigido virtuale fino a 32 TiB direttamente in un disco gestito di grandi dimensioni.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, si consiglia di usare il caricamento diretto per ripristinare i backup dei clienti a Managed Disks. Se si sta caricando un disco rigido virtuale da un computer esterno ad Azure, la velocità con dipende dalla larghezza di banda locale. Se si usa una macchina virtuale di Azure, la larghezza di banda sarà identica a quella degli HDD standard.

Attualmente, il caricamento diretto è supportato per HDD standard, unità SSD standard e Managed disks SSD Premium. Non è ancora supportata per le unità SSD ultra.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- Un file VHD, archiviato localmente
- Se si intende caricare un disco rigido virtuale da-PEM: Un VHD [preparato per Azure](../windows/prepare-for-upload-vhd-image.md), archiviato localmente.
- In alternativa, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuoto

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:

- ReadToUpload, il che significa che il disco è pronto per ricevere un caricamento, ma non è stata generata alcuna [firma di accesso sicuro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e che la firma di accesso condiviso è stata generata.

In uno di questi Stati, il disco gestito verrà fatturato in base ai [prezzi standard di HDD](https://azure.microsoft.com/pricing/details/managed-disks/), indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come S10. Questo valore sarà true fino `revoke-access` a quando non viene chiamato sul disco gestito, operazione necessaria per il fissaggio del disco a una macchina virtuale.

Prima di poter creare un HDD standard vuoto per il caricamento, è necessario avere le dimensioni del file VHD da caricare, in byte. Per ottenere questo valore, è possibile usare `wc -c <yourFileName>.vhd` o `ls -al <yourFileName>.vhd`. Questo valore viene usato quando si specifica il parametro **--upload-size-bytes** .

Creare un HDD standard vuoto per il caricamento specificando il parametro **--for-upload** e il parametro **--upload-size-bytes** in un cmdlet di [creazione disco](/cli/azure/disk#az-disk-create) :

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se si vuole caricare un'unità SSD Premium o un'unità SSD standard, sostituire **standard_lrs** con **premium_LRS** o **standardssd_lrs**. Ultra SSD non è ancora supportata.

A questo punto è stato creato un disco gestito vuoto configurato per il processo di caricamento. Per caricare un disco rigido virtuale sul disco, è necessario disporre di una firma di accesso condiviso scrivibile, in modo che sia possibile farvi riferimento come destinazione per il caricamento.

Per generare una firma di accesso condiviso scrivibile del disco gestito vuoto, usare il comando seguente:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Valore restituito di esempio:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Carica VHD

Ora che si dispone di una firma di accesso condiviso per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy V10 per caricare il file VHD locale in un disco gestito specificando l'URI di firma di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva del [disco rigido standard](disks-types.md#standard-hdd)equivalente. Se, ad esempio, si dispone di una dimensione equivalente a S4, sarà presente una velocità effettiva massima di 60 MiB/s. Tuttavia, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva di un massimo di 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Se la firma di accesso condiviso scade durante il caricamento e non `revoke-access` è ancora stata chiamata, è possibile ottenere una nuova firma di accesso `grant-access`condiviso per continuare il caricamento usando, di nuovo.

Una volta completato il caricamento e non è più necessario scrivere altri dati sul disco, revocare la firma di accesso condiviso. La revoca della firma di accesso condiviso modificherà lo stato del disco gestito e consentirà di aggiungere il disco a una macchina virtuale.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile eseguire la copia all'interno della stessa area o tra più aree (in un'altra area).

Lo script seguente consente di eseguire questa operazione per l'utente, il processo è simile a quello descritto in precedenza, con alcune differenze poiché si sta lavorando con un disco esistente.

> [!IMPORTANT]
> È necessario aggiungere un offset di 512 quando si specificano le dimensioni del disco in byte di un disco gestito da Azure. Questo perché Azure omette il piè di pagina quando restituisce le dimensioni del disco. Se non si esegue questa operazione, la copia avrà esito negativo. Lo script seguente esegue già questa operazione.

Sostituire il `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>` e `<yourTargetLocationHere>` (un esempio di un valore location è uswest2) con i valori, quindi eseguire lo script seguente per copiare un disco gestito.

```bash
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -n $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n <sourceDiskNameHere> -g $sourceRG --duration-in-seconds 86400 --query [acessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato caricato correttamente un disco rigido virtuale in un disco gestito, è possibile collegarlo a una macchina virtuale e iniziare a usarlo.

Per informazioni su come aggiungere un disco a una macchina virtuale, vedere l'articolo sull'argomento: [Aggiungere un disco a una VM Linux](add-disk.md).
