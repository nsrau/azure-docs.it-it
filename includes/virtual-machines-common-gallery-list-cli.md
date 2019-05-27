---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1e78109472668c0f9a73af6430253a0d709979af
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156136"
---
## <a name="using-rbac-to-share-images"></a>Usare RBCA per condividere immagini

È possibile condividere immagini tra le sottoscrizioni che usano controllo di accesso basato sui ruoli (RBAC). Qualsiasi utente che disponga di autorizzazioni di lettura per la versione immagine, perfino tra sottoscrizioni, riuscirà a distribuire una macchina virtuale usando la versione immagine.

Per altre informazioni su come condividere risorse usando RBCA, vedere [Gestione dell'accesso usando RBCA e L'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informazioni di un elenco

Ottenere il percorso, lo stato e altre informazioni sulle raccolte immagini disponibili tramite [elenco az sig](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Elencare le definizioni delle immagini in una raccolta, tra cui informazioni sul tipo di sistema operativo e sullo stato, usando [elenco di definizione dell'immagine di az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Elencare le versioni immagine condivise in una raccolta, usando [elenco versione immagine di az sig](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myImageDefinition -o table
```

Ottenere l'ID di una versione immagine usando [mostra versione dell'immagine di az sig](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   -g myGalleryRG \
   -r myGallery \
   -i myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```