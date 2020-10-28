---
title: includere file
description: includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6432c125c4fedd962faa28a4c84c7494300b0472
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755436"
---
## <a name="list-information"></a>Informazioni di un elenco

Ottenere il percorso, lo stato e altre informazioni sulle raccolte immagini disponibili tramite [elenco az sig](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Elencare le definizioni delle immagini in una raccolta, tra cui informazioni sul tipo di sistema operativo e sullo stato, usando [elenco di definizione dell'immagine di az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Elencare le versioni immagine condivise in una raccolta, usando [elenco versione immagine di az sig](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Ottenere l'ID di una versione immagine usando [mostra versione dell'immagine di az sig](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
