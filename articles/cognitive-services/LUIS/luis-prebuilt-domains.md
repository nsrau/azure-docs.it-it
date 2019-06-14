---
title: Domini predefiniti per Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS include un set di domini predefiniti per aggiungere rapidamente scenari di conversazione utente comuni.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: e1b99396c4739dc6f1b7a4da0164553d4c25ef3c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60198926"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Aggiungere domini predefiniti per scenari di utilizzo comuni 

LUIS include un set di finalità predefinite disponibili nei domini predefiniti per aggiungere rapidamente finalità ed espressioni comuni. Si tratta di un modo semplice e rapido per aggiungere funzionalità all'app client di conversazione senza dover progettare i modelli per tali funzionalità. 

## <a name="add-a-prebuilt-domain"></a>Aggiungere un dominio predefinito

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Nella pagina **Intents** (Finalità) selezionare **Add prebuilt domains** (Aggiungi domini predefiniti) dalla barra degli strumenti in basso a sinistra. 

1. Selezionare la finalità **Calendar** (Calendario) e quindi il pulsante **Add domain** (Aggiungi dominio).

    ![Aggiunta del dominio predefinito Calendar](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Selezionare **Intents** (Finalità) nel pannello di navigazione a sinistra per visualizzare le finalità di Calendar. Ogni finalità di questo dominio è preceduta da `Calendar.`. Oltre alle espressioni, all'app vengono aggiunte due entità per questo dominio, ovvero `Calendar.Location` e `Calendar.Subject`. 

## <a name="train-and-publish"></a>Eseguire il training e pubblicare l'app

1. Dopo avere aggiunto il dominio, eseguire il training dell'app selezionando **Train** (Training) nella barra degli strumenti in alto a destra. 

1. Nella barra degli strumenti superiore selezionare l'azione **Publish** (Pubblica). Eseguire la pubblicazione in **Production** (Produzione). 

1. Quando viene visualizzata la notifica verde di operazione riuscita, selezionare il collegamento **Refer to the list of endpoints** (Fare riferimento all'elenco degli endpoint) per visualizzare gli endpoint.

1. Selezionare un endpoint. Viene aperta una nuova scheda del browser che contiene tale endpoint. Mantenere aperta la scheda del browser e passare alla sezione **Test**.

## <a name="test"></a>Test

Per testare la nuova finalità nell'endpoint, aggiungere un valore per il parametro **q**: `Schedule a meeting with John Smith in Seattle next week`.

LUIS restituisce la finalità e l'oggetto corretti della riunione:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni di riferimento sui domini predefiniti](./luis-reference-prebuilt-domains.md)
