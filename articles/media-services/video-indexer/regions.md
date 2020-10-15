---
title: Aree in cui è disponibile Video Indexer - Azure
titleSuffix: Azure Media Services
description: Questo articolo illustra le aree di Azure in cui è disponibile Video Indexer di servizi multimediali di Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530939"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Aree di Azure in cui esiste Video Indexer

Le API di Video Indexer contengono un parametro **location** che è consigliabile impostare sull'area di Azure a cui deve essere indirizzata la chiamata. Questa deve essere un'[area di Azure in cui è disponibile Video Indexer](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Percorsi

Al `location` parametro deve essere assegnato il nome del codice dell'area di Azure come valore. Se si usa Video Indexer in modalità di anteprima, è necessario inserire `"trial"` come valore. `trial` è il valore predefinito per il `location` parametro. In caso contrario, per ottenere il nome in codice dell'area di Azure in cui si trova l'account e che la chiamata deve essere indirizzata a, è possibile usare il portale di Azure o eseguire un comando dell'interfaccia della riga di comando di [Azure](/cli/azure) .

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al sito Web di [Video Indexer](https://www.videoindexer.ai/).
1. Selezionare **account utente** nell'angolo superiore destro della pagina.
1. Trovare il percorso dell'account nell'angolo superiore destro.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Posizione":::
    
###  <a name="cli-command"></a>Comando dell'interfaccia della riga di comando

```azurecli-interactive
az account list-locations
```

Dopo avere eseguito la riga precedente, viene visualizzato un elenco di tutte le aree di Azure. Passare all'area di Azure con il *displayName* desiderato e usare il valore *name* per il parametro **location**.

Ad esempio, per l'area di Azure Stati Uniti occidentali 2 (indicata di seguito), usare "westus2" per il parametro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Passaggi successivi

- [Customize Language model using APIs](customize-language-model-with-api.md) (Personalizzare il modello linguistico usando le API)
- [Personalizzare il modello di Marchi tramite API](customize-brands-model-with-api.md)
- [Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md)
