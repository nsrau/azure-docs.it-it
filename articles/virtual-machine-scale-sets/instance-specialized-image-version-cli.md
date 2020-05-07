---
title: Creare un set di scalabilità da una versione di immagine specializzata usando l'interfaccia della riga di comando di Azure
description: Creare un set di scalabilità usando una versione di immagine specializzata in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 5de9fe7c81059c56c99a55ca066e186cbf83c50f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796980"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Creare un set di scalabilità usando una versione di immagine specializzata con l'interfaccia della riga di comando di Azure

Creare un set di scalabilità da una [versione di immagine specializzata](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) archiviata in una raccolta di immagini condivise. Se si vuole creare un set di scalabilità usando una versione di immagine generalizzata, vedere [creare una macchina virtuale da una versione di immagine generalizzata](instance-generalized-image-version-cli.md).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.4.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

Sostituire i nomi delle risorse in base alle esigenze in questo esempio. 

Elencare le definizioni di immagine in una raccolta usando [AZ sig Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) per visualizzare il nome e l'ID delle definizioni.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Per creare un set di [`az vmss create`](/cli/azure/vmss#az-vmss-create) scalabilità `--specialized` , usare il parametro per indicare che l'immagine è un'immagine specializzata.

Usare l'ID di definizione dell' `--image` immagine per per creare le istanze del set di scalabilità dalla versione più recente dell'immagine disponibile. È anche possibile creare le istanze del set di scalabilità da una versione specifica fornendo l'ID versione dell' `--image`immagine per. Tenere presente che l'uso di una versione di immagine specifica indica che l'automazione potrebbe non riuscire se la versione di immagine specifica non è disponibile perché è stata eliminata o rimossa dall'area. È consigliabile usare l'ID di definizione dell'immagine per creare la nuova macchina virtuale, a meno che non sia necessaria una versione di immagine specifica.

In questo esempio vengono create istanze dalla versione più recente dell'immagine di *myImageDefinition* .

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --Specialized
```


## <a name="next-steps"></a>Passaggi successivi
Il [Generatore di immagini di Azure (anteprima)](../virtual-machines/linux/image-builder-overview.md) consente di automatizzare la creazione della versione di immagine. è anche possibile usarla per aggiornare e [creare una nuova versione dell'immagine da una versione di immagine esistente](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

È anche possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)



