---
title: Creare una VM da un'immagine generalizzata usando l'interfaccia della riga di comando di Azure
description: Creare una macchina virtuale da una versione di immagine generalizzata usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 090b041c605c2328add8b46a97b6f151bae268c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501083"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Creare una macchina virtuale da una versione di immagine generalizzata usando l'interfaccia della riga di comando

Creare una macchina virtuale da una [versione di immagine generalizzata](./linux/shared-image-galleries.md#generalized-and-specialized-images) archiviata in una raccolta di immagini condivise. Se si vuole creare una VM usando un'immagine specializzata, vedere [creare una VM da un'immagine specializzata](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Ottenere l'ID immagine

Elencare le definizioni di immagine in una raccolta usando [AZ sig Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) per visualizzare il nome e l'ID delle definizioni.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm#az-vm-create). Per usare la versione più recente dell'immagine, impostare sull' `--image` ID della definizione dell'immagine. 

Sostituire i nomi delle risorse di questo esempio secondo necessità. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

È anche possibile usare una versione specifica usando l'ID versione dell'immagine per il `--image` parametro. Ad esempio, per usare l'immagine versione *1.0.0* , digitare: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Passaggi successivi

Il [Generatore di immagini di Azure (anteprima)](./linux/image-builder-overview.md) consente di automatizzare la creazione della versione di immagine. è anche possibile usarla per aggiornare e [creare una nuova versione dell'immagine da una versione di immagine esistente](./linux/image-builder-gallery-update-image-version.md). 
