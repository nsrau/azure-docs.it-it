---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180159"
---
## <a name="update-resources"></a>Aggiornare le risorse

Esistono alcune limitazioni su ciò che può essere aggiornato. Gli elementi seguenti possono essere aggiornati: 

Raccolta di immagini condivise:
- Descrizione

Definizione delle immagini:
- VCPU consigliati
- Memoria consigliata
- Descrizione
- Data di scadenza

Versione immagine:
- Conteggio di repliche a livello di area
- Aree di destinazione
- Esclusione dalla versione più recente
- Data di scadenza

Se si prevede di aggiunta di aree di replica, non eliminare l'immagine gestita di origine. L'immagine gestita di origine è necessaria per la replica versione immagine in aree aggiuntive. 

Aggiornare la descrizione di una raccolta usando ([aggiornamento sig az](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aggiornare la descrizione di una definizione di immagini usando [update definizione dell'immagine di az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aggiornare una versione di immagine per aggiungere un'area per la replica in uso [update versione dell'immagine di az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Questa modifica potrebbe richiedere qualche minuto come l'immagine vengono replicato nella nuova area.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Eliminare le risorse

È necessario eliminare le risorse in ordine inverso, eliminando innanzitutto la versione dell'immagine. Dopo aver eliminato tutte le versioni di immagine, è possibile eliminare la definizione dell'immagine. Dopo aver eliminato tutte le definizioni di immagine, è possibile eliminare la raccolta. 

Eliminare una versione di immagine usando [Elimina versione immagine sig az](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Eliminare una definizione di immagini usando [immagine sig az-definition delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Eliminare un'immagine della raccolta usando [sig az eliminare](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
