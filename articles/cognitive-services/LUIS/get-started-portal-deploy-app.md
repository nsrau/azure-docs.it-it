---
title: 'Guida introduttiva: Distribuire app con il portale LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Quando l'app è pronta a restituire stime di espressioni a un'applicazione client, ad esempio un chatbot, è necessario distribuirla nell'endpoint di stima. Questo argomento di avvio rapido illustra come distribuire un'applicazione creando una risorsa endpoint di stima, assegnandola all'app e quindi eseguendo il training e la pubblicazione dell'app.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259721"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Guida introduttiva: Distribuire un'app nel portale LUIS

Quando l'app è pronta a restituire stime di espressioni a un'applicazione client, ad esempio un chatbot, è necessario distribuirla nell'endpoint di stima. 

Questo argomento di avvio rapido illustra come distribuire un'applicazione creando una risorsa endpoint di stima, assegnandola all'app e quindi eseguendo il training e la pubblicazione dell'app. 

## <a name="prerequisites"></a>Prerequisiti

* [Sottoscrizione di Azure](https://azure.microsoft.com/free).
* Completare il precedente [argomento di avvio rapido sul portale](get-started-portal-build-app.md) oppure [scaricare e importare l'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Creare la risorsa endpoint

La risorsa endpoint di stima viene creata nel portale di Azure. Dovrà essere usata solo per le query di stima dell'endpoint. Non usarla per apportare modifiche all'app. 

1. Accedere al **[portale di Azure](https://ms.portal.azure.com/)**. 

1. Selezionare il segno **+** verde nel pannello in alto a sinistra e cercare `Cognitive Services` nel Marketplace, quindi selezionarli. 

1. Configurare la sottoscrizione con le impostazioni seguenti:

    |Impostazione|Valore|Scopo|
    |--|--|--|
    |NOME|`my-cognitive-service-resource`|Il nome della risorsa di Azure. Questo nome è necessario quando si assegna la risorsa all'app nel portale LUIS.|
    |Sottoscrizione|Sottoscrizione in uso|Selezionare una delle sottoscrizioni associate all'account.|
    |Località|**Stati Uniti occidentali**|L'area di Azure per questo gruppo di risorse.|
    |Piano tariffario|**S0**|Il piano tariffario predefinito per questa risorsa.|
    |Gruppo di risorse|`my-cognitive-service-resource-group`|Creare un nuovo gruppo di risorse per tutte le risorse dei servizi cognitivi. Quando le risorse non sono più necessarie, è possibile eliminare il gruppo per pulire la sottoscrizione. | 

    ![Scelta di API di Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Selezionare **Crea** per creare la risorsa di Azure. 

    La sezione successiva illustrerà come connettere questa nuova risorsa a un'app LUIS nel portale LUIS. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Assegnare la chiave della risorsa all'app LUIS nel portale LUIS

Ogni volta che si crea una nuova risorsa per LUIS, è necessario assegnarla all'app LUIS. In seguito non sarà più necessario eseguire questo passaggio a meno che non venga creata una nuova risorsa. Si potrebbe creare una nuova risorsa per espandere le aree dell'app oppure per supportare un numero più elevato di query di stima. 

1. Accedere al [portale LUIS](https://www.luis.ai) e scegliere l'app **myEnglishApp** nell'elenco.

1. Selezionare **Manage** (Gestisci) nel menu in alto a destra, quindi selezionare **Keys and endpoints** (Chiavi ed endpoint).

1. Per aggiungere il servizio LUIS, selezionare **Assegnare una risorsa +**.

    [![Assegnare una risorsa all'app](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Selezionare il tenant, la sottoscrizione e il nome della risorsa, quindi selezionare **Assign resource** (Assegna risorsa). 

    ![Assegnare una risorsa all'app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Trovare la nuova riga nella tabella e copiare l'URL endpoint. Per ottenere una stima, è consigliabile inoltrare una richiesta HTTP GET all'endpoint dell'API LUIS. 

## <a name="train-and-publish-the-app"></a>Eseguire il training dell'app e pubblicarla 

È consigliabile eseguire il training ogni volta che si è pronti a testare l'app. Pubblicare l'app ogni volta che si vuole rendere disponibile la versione sottoposta a training per le applicazioni client dal runtime dell'endpoint di stima. 

1. Se il training dell'app non è stato eseguito, selezionare **Train** (Esegui il training) nel menu in alto a destra.

1. Selezionare **Publish** (Pubblica) nel menu in alto a destra. Accettare le impostazioni predefinite dell'ambiente e selezionare **Publish**.

1. Quando viene visualizzata la barra di notifica verde di operazione riuscita nella parte superiore della finestra del browser, selezionare il collegamento **Refer to the list of endpoints** (Fare riferimento all'elenco degli endpoint). 

    ![Barra di notifica relativa all'app correttamente pubblicata nel browser](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Verrà visualizzata la pagina **Keys and Endpoint settings** (Impostazioni di chiavi e endpoint). Nella parte inferiore della pagina è riportato l'elenco di risorse assegnate e degli URL degli endpoint corrispondenti. 

1. Selezionare l'URL dell'endpoint associato al nuovo nome di risorsa. Verrà aperto un Web browser con un URL correttamente costruito per effettuare una richiesta **GET** al runtime dell'endpoint di stima. 

1. L'elemento `q=` alla fine dell'URL è l'abbreviazione di **query** ed è il punto in cui l'espressione dell'utente viene aggiunta alla richiesta GET. Dopo `q=`, immettere la stessa espressione dell'utente usata alla fine del precedente argomento di avvio rapido:

    ```Is there a form named hrf-234098```

    La risposta del browser è lo stesso codice JSON che riceverà l'applicazione client:

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

    Questa risposta fornisce più informazioni rispetto al riquadro **Test** predefinito dell'esercitazione precedente. Se si vuole vedere questo stesso livello di informazioni nel riquadro Test, è necessario pubblicare l'app. Quindi, nel riquadro Test, selezionare **Compare with published** (Confronta con pubblicata). Selezionare **Show JSON view** (Mostra visualizzazione JSON) nel riquadro di test dell'app pubblicata per vedere lo stesso codice JSON indicato nel passaggio precedente. In questo modo è possibile confrontare l'app attualmente in uso con l'app pubblicata nell'endpoint.

    [![Confrontare la versione dell'app in fase di modifica con quella pubblicata](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Pulire le risorse
Al termine di questo argomento di avvio rapido, scegliere **My apps** (Le mie app) dal menu di spostamento superiore. Quindi selezionare la casella di controllo a sinistra dell'app nell'elenco e selezionare **Delete** (Elimina) sulla barra degli strumenti contestuale sopra l'elenco. 

[![Eliminare l'app dall'elenco My apps](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Identificare finalità ed entità comuni](luis-tutorial-prebuilt-intents-entities.md)