---
title: Aggiungere finalità ed entità predefinite per estrarre i dati comuni in Language Understanding - Azure |Microsoft Docs
description: Informazioni su come usare finalità ed entità predefinite per estrarre i diversi tipi di dati di entità.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266380"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Usare finalità ed entità predefinite per gestire le finalità e i dati comuni
Aggiungere finalità ed entità predefinite all'app della guida introduttiva HumanResource per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
* Aggiungere finalità predefinite 
* Aggiungere le entità predefinite datetimeV2 e number
* Eseguire il training e pubblicare l'app
* Eseguire una query LUIS e ricevere una risposta di previsione

## <a name="before-you-begin"></a>Prima di iniziare
Se non si dispone dell'app HumanResource descritta nella guida introduttiva [Con dominio personalizzato](luis-quickstart-intents-only.md), [importare](create-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS][LUIS] dal repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json).

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
    * Utilities.Stop
    * Utilities.StartOver

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

2. Nella parte superiore destra del sito Web LUIS, selezionare il pulsante **Publish** (Pubblica) per accedere alla relativa pagina. Lo slot di produzione è selezionato per impostazione predefinita. Fare clic sul pulsante **Publish** (Pubblica) accanto allo slot di produzione. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web a conferma il completamento.

    Non è necessario creare una chiave LUIS nel portale di Azure prima di pubblicare o testare l'URL dell'endpoint. Ogni app LUIS dispone di una chiave di avvio gratuita. Tale chiave offre creazione illimitata e [alcuni riscontri di endpoint](luis-boundaries.md#key-limits). 

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

Aggiungendo finalità ed entità predefinite in modo facile e veloce, l'applicazione client può integrare la gestione delle conversazioni ed estrarre i datatype comuni. 

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sulle entità](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
