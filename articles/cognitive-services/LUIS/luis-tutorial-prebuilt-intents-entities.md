---
title: Aggiungere finalità ed entità predefinite per estrarre i dati comuni in Language Understanding - Azure |Microsoft Docs
description: Informazioni su come usare finalità ed entità predefinite per estrarre i diversi tipi di dati di entità.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 3fc2040e66f6fc649448d3241b01678b7bb7f214
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239036"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Esercitazione: 2. Aggiungere finalità ed entità predefinite
Aggiungere finalità ed entità predefinite all'app dell'esercitazione relativa alle risorse umane per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
* Aggiungere finalità predefinite 
* Aggiungere le entità predefinite datetimeV2 e number
* Eseguire il training e pubblicare l'app
* Eseguire una query LUIS e ricevere una risposta di previsione

## <a name="before-you-begin"></a>Prima di iniziare
Se non è disponibile l'app relativa alle [risorse umane](luis-quickstart-intents-only.md) descritta nell'esercitazione precedente, [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON nella nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website) dal repository [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) di Github.

Se si vuole mantenere l'app originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `prebuilts`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="add-prebuilt-intents"></a>Aggiungere finalità predefinite
LUIS fornisce diverse finalità predefinite per le intenzioni comuni degli utenti.  

1. Assicurarsi che l'app sia presente nella sezione **Build** (Compila) di LUIS. È possibile passare a questa sezione selezionando **Build** (Compila) nella barra dei menu in alto a destra. 

    [ ![Screenshot dell'app LUIS con il pulsante relativo alla compilazione evidenziato in alto a destra sulla barra di spostamento](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Selezionare **Add prebuilt domain intent** (Aggiungi finalità di dominio predefinita). 

    [ ![Screenshot della pagina delle finalità con il pulsante per l'aggiunta di una finalità di dominio predefinita evidenziato](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Cercare `Utilities`. 

    [![Screenshot della finestra di dialogo delle finalità predefinite con Utilities (Utilità) nella casella di ricerca](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Selezionare le finalità seguenti e quindi scegliere **Done** (Fine): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Aggiungere entità predefinite
LUIS fornisce varie entità predefinite per l'estrazione di dati comuni. 

1. Scegliere **Entities** (Entità) dal menu di spostamento a sinistra.

    [ ![Screenshot dell'elenco delle finalità con il pulsante relativo alle entità evidenziato nel menu di spostamento a sinistra](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Fare clic sul pulsante **Manage prebuilt entities** (Gestisci entità predefinite).

    [ ![Screenshot dell'elenco delle entità con il pulsante per la gestione delle entità predefinite evidenziato](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Selezionare **number** e **datetimeV2** nell'elenco delle entità predefinite e quindi scegliere **Done** (Fine).

    ![Screenshot dell'entità number selezionata nella finestra di dialogo relativa alle entità predefinite](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Eseguire il training e pubblicare l'app
1. Nella parte superiore destra del sito Web LUIS, selezionare il pulsante **Train** (Esegui il training). 

    ![Pulsante per l'esecuzione del training](./media/luis-quickstart-intents-only/train-button.png)

    Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web a conferma del completamento.

    ![Barra di stato di training completato](./media/luis-quickstart-intents-only/trained.png)

2. Nella parte superiore destra del sito Web LUIS, selezionare il pulsante **Publish** (Pubblica) per accedere alla relativa pagina. 

3. Lo slot di produzione è selezionato per impostazione predefinita. Fare clic sul pulsante **Publish** (Pubblica) accanto allo slot di produzione. La pubblicazione è completata quando la barra di stato verde nella parte superiore del sito Web conferma il completamento.

    Non è necessario creare una chiave di endpoint Language Understanding nel portale di Azure prima di pubblicare o testare l'URL dell'endpoint. Ogni app Language Understanding dispone di una chiave di avvio gratuita. Tale chiave offre creazione illimitata e [alcuni riscontri di endpoint](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Eseguire query sull'endpoint con un'espressione
Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL dell'endpoint nella barra degli indirizzi. Andare alla fine dell'URL nell'indirizzo e immettere `I want to cancel on March 3`. L'ultimo parametro della stringa di query è `q`, la **query** dell'espressione. 

Il risultato ha previsto la finalità Utilities.Cancel e ha estratto la data del 3 marzo e il numero 3. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

Sono presenti due valori per il 3 marzo, perché l'espressione non ha indicato se il 3 marzo è relativo al passato o al futuro. L'applicazione che chiama LUIS deve fare una supposizione o richiedere chiarimenti, se necessario. 

Aggiungendo finalità ed entità predefinite in modo facile e veloce, l'applicazione client può integrare la gestione delle conversazioni ed estrarre i datatype comuni. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app LUIS. A questo scopo, scegliere **My apps** (App personali) dal menu in alto a sinistra. Selezionare i puntini di sospensione (***...***) a destra del nome dell'app nell'elenco di app e quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'entità di espressione regolare all'app](luis-quickstart-intents-regex-entity.md)

