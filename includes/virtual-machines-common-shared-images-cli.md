---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 547e0b08b2a57b4b9192f11907f77160142d4466
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192126"
---
## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare l'esempio in questo articolo, è necessario disporre di un'immagine gestita esistente di una macchina virtuale generalizzata. Per altre informazioni, vedere [Esercitazione: Creare un'immagine personalizzata di una macchina virtuale di Azure con l'interfaccia della riga di comando di Azure versione 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Anteprima: Registrare la funzionalità

Raccolte di immagini condivise è disponibile in anteprima, ma è necessario registrare la funzionalità prima di poterla usare. Per registrare la funzionalità Raccolte di immagini condivise:

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

La registrazione della funzionalità può richiedere alcuni minuti. È possibile controllare lo stato di avanzamento usando:

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I nomi di raccolta devono essere univoci all'interno della sottoscrizione. Creare una raccolta di immagini usando [sig az create](/cli/azure/sig#az-sig-create). L'esempio seguente crea una raccolta denominata *myGallery* nelle *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine

Creare una definizione dell'immagine iniziale nella raccolta usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>Creare una versione di immagine 
 
Creare versioni dell'immagine in base alle esigenze usando [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). È necessario passare l'ID dell'immagine gestita da usare come baseline per creare la versione dell'immagine. È possibile usare [elenco di immagini di az](/cli/azure/image?view#az-image-list) per ottenere informazioni sulle immagini in un gruppo di risorse. In questo esempio, la versione dell'immagine è*1.0.0* e verranno create 5 repliche totali nelle aree *Stati Uniti centro-occidentali*, *Stati Uniti centro-meridionali* e Stati Uniti orientali 2*.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

