---
title: Entità predefinite per Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. Il supporto dell'entità predefinita varia a seconda delle impostazioni cultura dell'app LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: e3bd203c9ab1d6daaae04866cf195b3ca28c3078
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041558"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entità predefinite per riconoscere tipi di dati comuni

LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. Il supporto dell'entità predefinita varia a seconda delle impostazioni cultura dell'app LUIS. Per un elenco completo delle entità predefinite supportate da LUIS, incluso il supporto per le impostazioni cultura, consultare il [riferimento all'entità predefinita](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **builtin.datetime** è deprecata. Viene sostituita da [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), che consente il riconoscimento degli intervalli di date e ora, nonché il riconoscimento migliorato di date e ore ambigue.

## <a name="add-a-prebuilt-entity"></a>Aggiungere un'entità predefinita

1. Aprire l'app facendo clic sul relativo nome nella pagina **App personali** e selezionare **Entità** nel riquadro sinistro. 
2. Nella pagina **Entità** fare clic su **Manage prebuilt entities** (Gestisci entità predefinite).

3. Nella finestra di dialogo **Add prebuilt entities** (Aggiungi entità predefinite) fare clic sull'entità predefinita che si desidera aggiungere (ad esempio, "datetimeV2"). Fare quindi clic su **Salva**.

    ![Finestra di dialogo Add prebuilt entities (Aggiungi entità predefinite)](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Usare un'entità predefinita numero
Quando si include un'entità predefinita nell'applicazione, le relative stime vengono incluse nell'applicazione pubblicata. Si esegue il training preliminare del comportamento delle entità predefinite. **Non è possibile** applicare modifiche a questo comportamento. Seguire questa procedura per vedere in che modo funziona un'entità predefinita:

1. Aggiungere un'entità **numero** all'app, quindi [eseguire il training](luis-interactive-test.md) e [pubblicare](luis-how-to-publish-app.md) l'app.
2. Fare clic sull'URL dell'endpoint nella pagina **Pubblica app** per aprire l'endpoint LUIS in un Web browser. 
3. Accodare un'espressione all'URL che contiene un'espressione numerica. Ad esempio, è possibile digitare `buy two plane ticktets` e vedere come LUIS identifica `two` come entità `builtin.number` e `2` come suo valore nel campo `resolution`. Il campo `resolution` consente di risolvere i numeri e le date in una forma canonica che è più semplice da usare per l'applicazione client. 

    ![espressione nel browser contenente un'entità numero](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS è in grado di riconoscere in modo intelligente i numeri che non hanno un formato standard. Provare le diverse espressioni numeriche nelle espressioni per vedere ciò che LUIS restituisce.

L'esempio seguente mostra una risposta JSON di LUIS, che include la risoluzione del valore 24, per l'espressione "due dozzine".

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>Usare un'entità datetimeV2 predefinita
L'entità predefinita **datetimeV2** riconosce date, orari, intervalli di date e intervalli di tempo. Seguire questa procedura per vedere in che modo funziona l'entità predefinita `datetimeV2`:

1. Aggiungere un'entità **datetimeV2** all'app, quindi [eseguire il training](luis-interactive-test.md) e [pubblicare](luis-how-to-publish-app.md) l'app.
2. Fare clic sull'URL dell'endpoint nella pagina **Pubblica app** per aprire l'endpoint LUIS in un Web browser. 
3. Accodare un'espressione all'URL che contiene un intervallo di date. Ad esempio, è possibile digitare `book a flight tomorrow` e vedere come LUIS identifica `tomorrow` come entità `builtin.datetimeV2.date` e la data di domani come suo valore nel campo `resolution`. 

L'esempio seguente mostra quale potrebbe essere la risposta JSON di LUIS se oggi fosse il 31 ottobre 2017.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle entità predefinite](./luis-reference-prebuilt-entities.md)
