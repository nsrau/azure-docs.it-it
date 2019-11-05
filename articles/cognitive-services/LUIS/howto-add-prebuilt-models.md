---
title: Modelli predefiniti per Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS include un set di modelli predefiniti per aggiungere rapidamente scenari utente comuni e di conversazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507781"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Aggiungere modelli predefiniti per scenari di utilizzo comuni 

LUIS include un set di finalità predefinite disponibili nei domini predefiniti per aggiungere rapidamente finalità ed espressioni comuni. Si tratta di un modo semplice e rapido per aggiungere funzionalità all'app client di conversazione senza dover progettare i modelli per tali funzionalità. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Aggiungere un dominio predefinito

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Nella pagina **Intents** (Finalità) selezionare **Add prebuilt domains** (Aggiungi domini predefiniti) dalla barra degli strumenti in basso a sinistra. 

1. Selezionare la finalità **Calendar** (Calendario) e quindi il pulsante **Add domain** (Aggiungi dominio).

    ![Aggiunta del dominio predefinito Calendar](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Selezionare **Intents** (Finalità) nel pannello di navigazione a sinistra per visualizzare le finalità di Calendar. Ogni finalità di questo dominio è preceduta da `Calendar.`. Oltre alle espressioni, all'app vengono aggiunte due entità per questo dominio, ovvero `Calendar.Location` e `Calendar.Subject`. 

### <a name="train-and-publish"></a>Eseguire il training e pubblicare l'app

1. Dopo avere aggiunto il dominio, eseguire il training dell'app selezionando **Train** (Training) nella barra degli strumenti in alto a destra. 

1. Nella barra degli strumenti superiore selezionare l'azione **Publish** (Pubblica). Eseguire la pubblicazione in **Production** (Produzione). 

1. Quando viene visualizzata la notifica verde di operazione riuscita, selezionare il collegamento **Refer to the list of endpoints** (Fare riferimento all'elenco degli endpoint) per visualizzare gli endpoint.

1. Selezionare un endpoint. Viene aperta una nuova scheda del browser che contiene tale endpoint. Mantenere aperta la scheda del browser e passare alla sezione **Test**.

### <a name="test"></a>Test

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

## <a name="add-a-prebuilt-intent"></a>Aggiungere una finalità predefinita

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Nella pagina **Intents** (Finalità) selezionare **Add prebuilt intent** (Aggiungi finalità predefinita) dalla barra degli strumenti sopra l'elenco delle finalità. 

1. Selezionare la finalità **Utilities.Cancel** nella finestra di dialogo popup. 

    ![Aggiunta della finalità predefinita](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Selezionare il pulsante **Done** (Fine).

### <a name="train-and-test"></a>Eseguire il training e il test

1. Dopo avere aggiunto la finalità, eseguire il training dell'app selezionando **Train** (Training) nella barra degli strumenti in alto a destra. 

1. Per testare la nuova finalità, seleziona **Test** nella barra degli strumenti a destra. 

1. Nella casella di testo immettere le espressioni per l'annullamento:

    |Espressione di test|Punteggio di stima|
    |--|:--|
    |I want to cancel my flight.|0.67|
    |Cancel the purchase.|0.52|
    |Cancel the meeting.|0.56|

    ![Test della finalità predefinita](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Aggiungere un'entità predefinita

1. Aprire l'app facendo clic sul relativo nome nella pagina **App personali** e selezionare **Entità** nel riquadro sinistro. 

1. Nella pagina **Entità** fare clic su **Aggiungi entità predefinite**.

1. Nella finestra di dialogo **Add prebuilt entities** (Aggiungi entità predefinite) selezionare l'entità predefinita datetimeV2. 

    ![Finestra di dialogo Add prebuilt entities (Aggiungi entità predefinite)](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selezionare **Operazione completata**. Dopo aver aggiunto l'entità, non è necessario eseguire il training dell'app. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Pubblicazione per la visualizzazione del modello predefinito dall'endpoint di stima

Il modo più semplice per visualizzare il valore di un modello predefinito consiste nell'eseguire una query dall'endpoint pubblicato. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Contrassegno di entità contenenti un token di entità predefinita
 Se è presente testo, ad esempio `HH-1234`, che si vuole contrassegnare come entità personalizzata _e_ al modello è stata aggiunta l'[entità numero predefinita](luis-reference-prebuilt-number.md), non sarà possibile contrassegnare l'entità personalizzata nel portale di LUIS. È possibile contrassegnarla con l'API. 

 Per contrassegnare questo tipo di token, di cui una parte è già contrassegnata con un'entità predefinita, rimuovere l'entità predefinita dall'app LUIS. Non è necessario eseguire il training dell'app. Contrassegnare quindi il token con l'entità personalizzata. Aggiungere quindi l'entità di nuovo all'app LUIS.

 Per un altro esempio, si consideri l'espressione come un elenco di preferenze di classe: `I want first year spanish, second year calculus, and fourth year english lit.` se per l'app LUIS è stato aggiunto il numero ordinale di precompilazione, `first`, `second`e `fourth` verranno già contrassegnati con i numeri ordinali. Se si vuole acquisire l'ordinale e la classe, è possibile creare un'entità composita ed eseguirne il wrapping nell'entità Ordinal predefinita e nell'entità personalizzata per il nome di classe.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Compilare il modello da CSV con le API REST](./luis-tutorial-node-import-utterances-csv.md)
