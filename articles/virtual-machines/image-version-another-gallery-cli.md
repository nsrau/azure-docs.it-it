---
title: Copiare una versione dell'immagine da un'altra raccolta usando l'interfaccia della riga di comando
description: Copiare una versione dell'immagine da un'altra raccolta con l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f1e3598f2a805dfc2ebf92395db6b7bf6b0b147a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494684"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Copiare un'immagine da un'altra raccolta usando l'interfaccia della riga di comando di Azure

Se nell'organizzazione sono presenti più raccolte, è anche possibile creare versioni di immagini da versioni di immagini esistenti archiviate in altre raccolte. È ad esempio possibile disporre di una raccolta di sviluppo e test per la creazione e il test di nuove immagini. Quando sono pronti per l'uso nell'ambiente di produzione, è possibile copiarli in una raccolta di produzione usando questo esempio. È anche possibile creare un'immagine da un'immagine in un'altra raccolta usando [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario disporre di una raccolta di origine, di una definizione di immagine e di una versione dell'immagine. È inoltre necessario disporre di una raccolta di destinazione. 

La versione dell'immagine di origine deve essere replicata nell'area in cui si trova la raccolta di destinazione. 

Quando si lavora in questo articolo, sostituire i nomi delle risorse laddove necessario.



## <a name="get-information-from-the-source-gallery"></a>Ottenere informazioni dalla raccolta di origine

Sono necessarie informazioni della definizione dell'immagine di origine, in modo che sia possibile crearne una copia nella nuova raccolta.

Elenca le informazioni sulle raccolte immagini disponibili usando [AZ sig list](/cli/azure/sig#az-sig-list) per trovare informazioni sulla raccolta di origine.

```azurecli-interactive 
az sig list -o table
```

Elencare le definizioni di immagine in una raccolta usando [AZ sig Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list). In questo esempio viene eseguita la ricerca di definizioni di *immagine nella raccolta denominata Gallery* nel gruppo di risorse *myGalleryRG* .

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Elencare le versioni di un'immagine in una raccolta, usando [AZ sig Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list) per trovare la versione dell'immagine che si vuole copiare nella nuova raccolta. In questo esempio vengono cercate tutte le versioni dell'immagine che fanno parte della definizione dell'immagine *myImageDefinition* .

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Una volta fornite tutte le informazioni necessarie, è possibile ottenere l'ID della versione dell'immagine di origine usando [AZ sig Image-Version Show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Creare la definizione dell'immagine 

È necessario creare una definizione di immagine che corrisponda alla definizione dell'immagine della versione dell'immagine di origine. È possibile visualizzare tutte le informazioni necessarie per ricreare la definizione di immagine nella nuova raccolta usando [AZ sig Image-Definition Show](/cli/azure/sig/image-definition#az-sig-image-definition-show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

L'output sarà simile al seguente:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Creare una nuova definizione di immagine, nella nuova raccolta, usando le informazioni dell'output precedente.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Creare la versione di immagine

Creare versioni usando [AZ Image Gallery create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). È necessario passare l'ID dell'immagine gestita da usare come baseline per creare la versione dell'immagine. È possibile usare [elenco di immagini di az](/cli/azure/image?view#az-image-list) per ottenere informazioni sulle immagini in un gruppo di risorse. 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio, la versione dell'immagine è *1.0.0* e verrà creata 1 replica nell'area *Stati Uniti centro-meridionali* e 1 replica nell'area *Stati Uniti orientali* con archiviazione con ridondanza della zona.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> Quando si crea la versione dell'immagine, è anche possibile archiviare l'immagine nell'archiviazione Premium, aggiungendo `--storage-account-type  premium_lrs`, oppure nell'[archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md), aggiungendo `--storage-account-type  standard_zrs`.
>

## <a name="next-steps"></a>Passaggi successivi

Creare una macchina virtuale da una versione di immagine [generalizzata](vm-generalized-image-version-cli.md) o [specializzata](vm-specialized-image-version-cli.md) .

Provare anche il [Generatore di immagini di Azure (anteprima)](./linux/image-builder-overview.md) per automatizzare la creazione della versione di immagine. è anche possibile usarlo per aggiornare e [creare una nuova versione dell'immagine da una versione di immagine esistente](./linux/image-builder-gallery-update-image-version.md). 

Per informazioni su come fornire informazioni sul piano di acquisto, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).
