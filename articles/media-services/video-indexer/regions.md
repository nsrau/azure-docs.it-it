---
title: Aree in cui è disponibile Video Indexer - Azure
titleSuffix: Azure Media Services
description: Questo articolo descrive le aree di Azure in cui è disponibile Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b67c89e02091d57b4f2dc901005c2dae36deafc2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838194"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Aree di Azure in cui esiste Video Indexer

Le API di Video Indexer contengono un parametro **location** che è consigliabile impostare sull'area di Azure a cui deve essere indirizzata la chiamata. Questa deve essere un'[area di Azure in cui è disponibile Video Indexer](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Località

Al parametro **location** deve essere assegnato come valore il nome del codice di un'area di Azure. Se si usa Video Indexer in modalità di anteprima, inserire *"trial"* come valore. In alternativa, per ottenere il nome del codice dell'area di Azure in cui si trova l'account e a cui deve essere indirizzata la chiamata, è possibile eseguire la riga seguente nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

Dopo avere eseguito la riga precedente, viene visualizzato un elenco di tutte le aree di Azure. Passare all'area di Azure con il *displayName* desiderato e usare il valore *name* per il parametro **location**.

Ad esempio, per l'area di Azure Stati Uniti occidentali 2 (indicata di seguito), usare "westus2" per il parametro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Passaggi successivi

- [Personalizzare il modello linguistico usando le API](customize-language-model-with-api.md)
- [Personalizzare il modello di Marchi tramite API](customize-brands-model-with-api.md)
- [Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md)
