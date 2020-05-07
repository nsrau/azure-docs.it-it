---
title: includere il file
description: Includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: de1a22ed6e9707767c0d097a9250f0bdd31414d5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788961"
---
## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. 

I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini.   I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [sig az create](/cli/azure/sig#az-sig-create). Nell'esempio seguente viene creato un gruppo di risorse denominato Gallery denominata *myGalleryRG* negli *Stati Uniti orientali*e *una raccolta denominata Gallery.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Condividere la raccolta

È possibile condividere immagini tra sottoscrizioni tramite il controllo degli accessi in base al ruolo (RBAC). È possibile condividere immagini nella raccolta, nella definizione dell'immagine o nella versione dell'immagine leve. Qualsiasi utente che disponga delle autorizzazioni di lettura per una versione dell'immagine, anche tra le sottoscrizioni, sarà in grado di distribuire una VM usando la versione dell'immagine.

Si consiglia di condividere con altri utenti a livello di raccolta. Per ottenere l'ID oggetto della raccolta, usare [AZ sig Show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Usare l'ID oggetto come ambito, insieme a un indirizzo di posta elettronica e [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create) per concedere a un utente l'accesso alla raccolta di immagini condivise. Sostituire `<email-address>` e `<gallery iD>` con le informazioni desiderate.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Per altre informazioni su come condividere risorse usando RBCA, vedere [Gestione dell'accesso usando RBCA e L'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).
