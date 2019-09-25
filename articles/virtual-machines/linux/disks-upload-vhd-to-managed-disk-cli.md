---
title: Caricare un VHD in Azure usando l'interfaccia della riga di comando di Azure
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure usando l'interfaccia della riga di comando di Azure.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: bd4d3b9b34f951896e838d5f6f50ca204d329568
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266601"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Caricare un VHD in Azure usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come caricare un disco rigido virtuale dal computer locale a un disco gestito di Azure. In precedenza era necessario seguire un processo più impegnativo che includeva la gestione temporanea dei dati in un account di archiviazione e la gestione dell'account di archiviazione. A questo punto, non è più necessario gestire un account di archiviazione o organizzare i dati per il caricamento di un disco rigido virtuale. Viene invece creato un disco gestito vuoto e viene caricato direttamente un disco rigido virtuale. Questo semplifica il caricamento di macchine virtuali locali in Azure e consente di caricare un disco rigido virtuale fino a 32 TiB direttamente in un disco gestito di grandi dimensioni.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, si consiglia di usare il caricamento diretto per ripristinare i backup dei clienti a Managed Disks. Se si sta caricando un disco rigido virtuale da un computer esterno ad Azure, la velocità con dipende dalla larghezza di banda locale. Se si usa una macchina virtuale di Azure, la larghezza di banda sarà identica a quella degli HDD standard.

Attualmente, il caricamento diretto è supportato per HDD standard, unità SSD standard e Managed disks SSD Premium. Non è ancora supportata per le unità SSD ultra.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione più recente [di AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- Un file VHD, archiviato localmente

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuoto

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato in modo specifico per questo processo di caricamento. Prima di crearne uno, è necessario conoscere informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:

- ReadToUpload, il che significa che il disco è pronto per ricevere un caricamento, ma non è stata generata alcuna [firma di accesso sicuro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, il che significa che il disco è pronto per ricevere un caricamento e che la firma di accesso condiviso è stata generata.

In uno di questi Stati, il disco gestito verrà fatturato in base ai [prezzi standard di HDD](https://azure.microsoft.com/pricing/details/managed-disks/), indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come S10. Questo valore sarà true fino `revoke-access` a quando non viene chiamato sul disco gestito, operazione necessaria per il fissaggio del disco a una macchina virtuale.

Creare un HDD standard vuoto per il caricamento specificando il parametro-- **for-upload** e il parametro **--upload-size-bytes** in un cmdlet di [creazione disco](/cli/azure/disk#az-disk-create) :

```azurecli-interactive
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se si vuole caricare un'unità SSD Premium o un'unità SSD standard, sostituire **standard_lrs** con **premium_LRS** o **standardssd_lrs**. Ultra SSD non è ancora supportata.

A questo punto è stato creato un disco gestito vuoto configurato per il processo di caricamento. Per caricare un disco rigido virtuale sul disco, è necessario disporre di una firma di accesso condiviso scrivibile, in modo che sia possibile farvi riferimento come destinazione per il caricamento.

Per generare una firma di accesso condiviso scrivibile del disco gestito vuoto, usare il comando seguente:

```azurecli-interactive
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

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Se la firma di accesso condiviso scade durante il caricamento e non `revoke-access` è ancora stata chiamata, è possibile ottenere una nuova firma di accesso `grant-access`condiviso per continuare il caricamento usando, di nuovo.

Una volta completato il caricamento e non è più necessario scrivere altri dati sul disco, revocare la firma di accesso condiviso. La revoca della firma di accesso condiviso modificherà lo stato del disco gestito e consentirà di aggiungere il disco a una macchina virtuale.

```azurecli-interactive
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato caricato correttamente un disco rigido virtuale in un disco gestito, è possibile collegarlo a una macchina virtuale e iniziare a usarlo.

Per informazioni su come aggiungere un disco a una macchina virtuale, vedere l'articolo sull'argomento: [Aggiungere un disco a una VM Linux](add-disk.md).
