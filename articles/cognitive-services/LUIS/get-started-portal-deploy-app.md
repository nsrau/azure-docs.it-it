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
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: ecae5c7db02436fe34fec19989f174504fd1e03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488713"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Guida introduttiva: Distribuire un'app nel portale LUIS

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]


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

1. Scegliere **Manage** (Gestisci) dal menu in alto a destra e quindi selezionare **Azure Resources** (Risorse di Azure).

1. Per aggiungere LUIS, selezionare **Add prediction resource** (Aggiungi risorsa di stima).

    <!-- TBD: get screenshot-->

1. Selezionare il tenant, la sottoscrizione e il nome della risorsa. Selezionare **Assign resource** (Assegnare una risorsa).

   ![Assegnare una risorsa all'app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Trovare la nuova riga nella tabella e copiare l'URL endpoint. Per ottenere una stima, è corretto inoltrare una richiesta `HTTP GET` all'endpoint dell'API LUIS.

## <a name="train-and-publish-the-app"></a>Eseguire il training dell'app e pubblicarla

Quando si è pronti per il test dell'app, eseguire il training. Pubblicare l'app ogni volta che si vuole rendere disponibile la versione attualmente sottoposta a training per le applicazioni client dal runtime dell'endpoint di stima di query.

1. Se il training dell'app non è stato eseguito, scegliere **Train** (Esegui il training) dal menu in alto a destra.

1. Scegliere **Publish** (Pubblica) dal menu in alto. Selezionare lo slot di produzione, quindi pubblicare.

1. Quando viene visualizzata la barra di notifica, la pubblicazione è stata completata.

1. Nella pagina **Risorse di Azure** della sezione Gestione è riportato l'elenco delle risorse assegnate e degli URL degli endpoint corrispondenti.

1. Copiare la query di esempio in una finestra del browser e aggiungere l'espressione utente come parametro `query`.

## <a name="prediction-endpoint-request"></a>Richiesta dell'endpoint di previsione

L'elemento `query=` alla fine dell'URL è l'abbreviazione di **query** ed è il punto in cui l'espressione dell'utente viene aggiunta alla richiesta GET. Dopo `query=`, immettere la stessa espressione dell'utente usata alla fine del precedente argomento di avvio rapido:

```Is there a form named hrf-234098```

Assicurarsi che la stringa di query includa le coppie seguenti:

* `show-all-intents=true`
* `verbose=true`

Il browser mostra la risposta:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Se si vuole vedere questo stesso livello di informazioni nel riquadro di test, è necessario pubblicare l'app. Dopo la pubblicazione dell'app, selezionare **Compare with published** (Confronta con pubblicata) nel riquadro di test. Selezionare **Show JSON view** (Mostra visualizzazione JSON) nel riquadro di test dell'app pubblicata per vedere lo stesso codice JSON indicato nel passaggio precedente. In questo modo è possibile confrontare l'app attualmente in uso con un'app pubblicata nell'endpoint.

[![Confrontare la versione dell'app in fase di modifica con quella pubblicata](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, scegliere **My apps** (Le mie app) dal menu di spostamento superiore. Selezionare dall'elenco la casella di controllo dell'app e quindi selezionare **Delete** (Elimina) sulla barra degli strumenti contestuale sopra l'elenco.

[![Eliminare l'app dall'elenco My apps (App personali)](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Identificare finalità ed entità comuni](luis-tutorial-prebuilt-intents-entities.md)
