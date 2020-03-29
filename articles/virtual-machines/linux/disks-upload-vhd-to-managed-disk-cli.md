---
title: Caricare un disco rigido virtuale usando l'interfaccia della riga di comando di AzureUpload a VHD using the Azure CLI
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando l'interfaccia della riga di comando di Azure tramite il caricamento diretto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062674"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Caricare un disco rigido virtuale in Azure usando l'interfaccia della riga di comando di AzureUpload a vhd to Azure using Azure CLI

Questo articolo illustra come caricare un disco rigido dal computer locale a un disco gestito di Azure.This article explains how to upload a vhd from your local machine to an Azure managed disk. In precedenza, era necessario seguire un processo più complesso che includeva la gestione temporanea dei dati in un account di archiviazione e la gestione di tale account di archiviazione. A questo punto, non è più necessario gestire un account di archiviazione o i dati della fase in esso contenuti per caricare un disco rigido virtuale. Si crea invece un disco gestito vuoto e vi si carica direttamente un disco rigido virtuale. Ciò semplifica il caricamento delle macchine virtuali locali in Azure e consente di caricare un disco rigido fino a 32 TiB direttamente in un disco gestito di grandi dimensioni.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, è consigliabile usare il caricamento diretto per ripristinare i backup dei clienti nei dischi gestiti. Se si carica un disco rigido virtuale da un computer esterno ad Azure, le velocità dipenderanno dalla larghezza di banda locale. Se si usa una macchina virtuale di Azure, la larghezza di banda sarà la stessa degli HDD standard.

Attualmente, il caricamento diretto è supportato per i dischi gestiti HDD standard, SSD standard e SSD premium. Non è ancora supportato per gli SSD ultra.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)
- Un file vhd, memorizzato localmente
- Se si intende caricare un disco rigido virtuale da locale: un disco rigido a dimensione fissa [preparato per Azure,](../windows/prepare-for-upload-vhd-image.md)archiviato in locale.
- In alternativa, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuotoCreate an empty managed disk

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere alcune informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:This kind of managed disk has two unique states:

- ReadToUpload, che significa che il disco è pronto per ricevere un caricamento ma non è stata generata alcuna firma di [accesso sicuro.](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e la sAS è stata generata.

In uno di questi stati, il disco gestito verrà fatturato al [prezzo dell'HDD standard,](https://azure.microsoft.com/pricing/details/managed-disks/)indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come Un S10. Questo valore sarà `revoke-access` vero fino a quando non verrà chiamato sul disco gestito, che è necessario per collegare il disco a una macchina virtuale.

Prima di poter creare un hdD standard vuoto per il caricamento, è necessario disporre della dimensione del file del disco rigido virtuale che si desidera caricare, in byte. Per ottenere questo, è `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`possibile utilizzare uno o . Questo valore viene utilizzato quando si specifica il parametro **--upload-size-bytes.**

Creare un disco rigido standard vuoto per il caricamento specificando sia il parametro **-–for-upload** che il parametro **--upload-size-bytes** in un cmdlet [disk create:](/cli/azure/disk#az-disk-create)

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se si desidera caricare un SSD premium o un SSD standard, sostituire **standard_lrs** con **premium_LRS** o **standardssd_lrs**. Ultra SSD non è ancora supportato.

È stato creato un disco gestito vuoto configurato per il processo di caricamento. Per caricare un disco rigido virtuale sul disco, è necessaria una sAS scrivibile, in modo da potervi fare riferimento come destinazione per il caricamento.

Per generare una sAS scrivibile del disco gestito vuoto, utilizzare il comando seguente:

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Esempio di valore restituito:Sample returned value:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Caricare vhd

Ora che si dispone di una sAS per il disco gestito vuoto, è possibile usarla per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy v10 per caricare il file VHD locale su un disco gestito specificando l'URI di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva [dell'HDD standard](disks-types.md#standard-hdd)equivalente. Ad esempio, se si dispone di una dimensione che equivale a S4, si disputa una velocità effettiva fino a 60 MiB/s. Ma, se si dispone di una dimensione che equivale a S70, si avrà una velocità effettiva fino a 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Al termine del caricamento e non è più necessario scrivere altri dati sul disco, revocare la server di accesso sAS. La revoca della chiamata a chiamata accesso la chiamata a accesso locale modificherà lo stato del disco gestito e consentirà di collegare il disco a una macchina virtuale.

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
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

Dopo aver caricato correttamente un disco rigido virtuale su un disco gestito, è possibile collegare il disco come disco dati a una [macchina virtuale esistente](add-disk.md) o collegare il disco a una macchina virtuale come disco del sistema [operativo](upload-vhd.md#create-the-vm)per creare una nuova macchina virtuale. 

