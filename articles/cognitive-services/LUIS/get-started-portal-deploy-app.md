---
title: "Guida introduttiva: Distribuire un'app con il portale LUIS"
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire un'app LUIS nell'endpoint di stima quando l'app è pronta a restituire stime di espressioni a un'applicazione client, ad esempio un chatbot. Questo argomento di avvio rapido illustra come distribuire un'applicazione creando una risorsa endpoint di stima, assegnandola all'app e quindi eseguendo il training e la pubblicazione dell'app.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 5c310c1943eaf23423be873c6172e27c621fe109
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564072"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Guida introduttiva: Distribuire un'app nel portale LUIS

Quando l'app LUIS è pronta a restituire stime di espressioni a un'applicazione client, ad esempio un chatbot, è necessario distribuirla nell'endpoint di stima.

In questo argomento di avvio rapido viene illustrato come distribuire un'applicazione. Verrà creata una risorsa endpoint di stima, tale risorsa verrà assegnata all'app e quindi verrà eseguito il training e la pubblicazione dell'app.

## <a name="prerequisites"></a>Prerequisiti

* Ottenere una [sottoscrizione di Azure](https://azure.microsoft.com/free).
* Completare il precedente [argomento di avvio rapido sul portale](get-started-portal-build-app.md) oppure [scaricare e importare l'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Creare la risorsa endpoint

La risorsa endpoint di stima viene creata nel portale di Azure. Dovrà essere usata solo per le query di stima dell'endpoint. Non usarla per apportare modifiche all'app.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).

1. Selezionare il segno **+** verde nel pannello in alto a sinistra. Cercare `Cognitive Services` nel marketplace e selezionarlo.

1. Configurare la sottoscrizione con le impostazioni seguenti:

   |Impostazione|Valore|Scopo|
   |--|--|--|
   |NOME|`my-cognitive-service-resource`|Il nome della risorsa di Azure. Questo nome è necessario quando si assegna la risorsa all'app nel portale LUIS.|
   |Subscription|Sottoscrizione in uso|Selezionare una delle sottoscrizioni associate all'account.|
   |Location|**Stati Uniti occidentali**|L'area di Azure per questa risorsa.|
   |Piano tariffario|**S0**|Il piano tariffario predefinito per questa risorsa.|
   |Resource group|`my-cognitive-service-resource-group`|Creare un nuovo gruppo di risorse per tutte le risorse dei servizi cognitivi. Quando le risorse non sono più necessarie, è possibile eliminare il gruppo per pulire la sottoscrizione. |
   | | | |

   ![Scelta dell'API di Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Selezionare **Crea** per creare la risorsa di Azure.

   La sezione successiva illustrerà come connettere questa nuova risorsa a un'app LUIS nel portale LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Assegnare la chiave della risorsa all'app LUIS nel portale LUIS

Ogni volta che si crea una nuova risorsa per LUIS, è necessario assegnarla all'app LUIS. Dopo l'assegnazione, non sarà più necessario eseguire questo passaggio a meno che non venga creata una nuova risorsa. Si potrebbe creare una nuova risorsa per espandere le aree dell'app oppure per supportare un numero più elevato di query di stima.

1. Accedere al [portale LUIS](https://www.luis.ai) e scegliere l'app **myEnglishApp** dall'elenco.

1. Scegliere **Manage** (Gestisci) dal menu in alto a destra e quindi **Keys and endpoints** (Chiavi ed endpoint).

1. Per aggiungere LUIS, selezionare **Assign Resource +** (Assegnare una risorsa +).

   [![Assegnare una risorsa all'app](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Selezionare il tenant, la sottoscrizione e il nome della risorsa. Selezionare **Assign resource** (Assegnare una risorsa).

   ![Assegnare una risorsa all'app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Trovare la nuova riga nella tabella e copiare l'URL endpoint. Per ottenere una stima, è corretto inoltrare una richiesta `HTTP GET` all'endpoint dell'API LUIS.

## <a name="train-and-publish-the-app"></a>Eseguire il training dell'app e pubblicarla

Quando si è pronti per il test dell'app, eseguire il training. Pubblicare l'app ogni volta che si vuole rendere disponibile la versione attualmente sottoposta a training per le applicazioni client dal runtime dell'endpoint di stima di query.

1. Se il training dell'app non è stato eseguito, scegliere **Train** (Esegui il training) dal menu in alto a destra.

1. Scegliere **Publish** (Pubblica) dal menu in alto. Accettare le impostazioni predefinite dell'ambiente e selezionare **Publish**.

1. Quando viene visualizzata la barra di notifica verde di operazione riuscita nella parte superiore della finestra del browser, selezionare **Refer to the list of endpoints** (Fare riferimento all'elenco degli endpoint).

   ![Barra di notifica relativa all'app correttamente pubblicata nel browser](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Nella parte inferiore della pagina **Keys and Endpoint settings** (Impostazioni di chiavi ed endpoint) è riportato l'elenco delle risorse assegnate e degli URL degli endpoint corrispondenti.

1. Selezionare l'URL dell'endpoint associato al nuovo nome di risorsa. Verrà aperto un Web browser con un URL correttamente costruito per effettuare una richiesta `GET` al runtime dell'endpoint di stima.

1. L'elemento `q=` alla fine dell'URL è l'abbreviazione di **query** ed è il punto in cui l'espressione dell'utente viene aggiunta alla richiesta GET. Dopo `q=`, immettere la stessa espressione dell'utente usata alla fine del precedente argomento di avvio rapido:

    ```Is there a form named hrf-234098```

    Il browser visualizzerà la risposta, che è lo stesso codice JSON che verrà ricevuto dall'applicazione client:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Questa risposta fornisce più informazioni rispetto al riquadro di test predefinito dell'esercitazione precedente. Se si vuole vedere questo stesso livello di informazioni nel riquadro di test, è necessario pubblicare l'app. Dopo la pubblicazione dell'app, selezionare **Compare with published** (Confronta con pubblicata) nel riquadro di test. Selezionare **Show JSON view** (Mostra visualizzazione JSON) nel riquadro di test dell'app pubblicata per vedere lo stesso codice JSON indicato nel passaggio precedente. In questo modo è possibile confrontare l'app attualmente in uso con un'app pubblicata nell'endpoint.

    [![Confrontare la versione dell'app in fase di modifica con quella pubblicata](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, scegliere **My apps** (Le mie app) dal menu di spostamento superiore. Selezionare dall'elenco la casella di controllo dell'app e quindi selezionare **Delete** (Elimina) sulla barra degli strumenti contestuale sopra l'elenco.

[![Eliminare l'app dall'elenco My apps (App personali)](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Identificare finalità ed entità comuni](luis-tutorial-prebuilt-intents-entities.md)
