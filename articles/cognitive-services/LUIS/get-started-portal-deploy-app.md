---
title: "Guida introduttiva: Distribuire un'app con il portale LUIS"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come distribuire un'app creando una risorsa endpoint di previsione, assegnando la risorsa, eseguendo il training e quindi pubblicando l'app.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 65af2caa2272549b5ad562ff3c38b90e3ea43fd5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278551"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Guida introduttiva: Distribuire un'app nel portale LUIS

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

Quando l'app LUIS è pronta a restituire stime di espressioni a un'applicazione client, ad esempio un chatbot, è necessario distribuirla nell'endpoint di stima.

In questo argomento di avvio rapido viene illustrato come distribuire un'applicazione. Verrà creata una risorsa endpoint di stima, tale risorsa verrà assegnata all'app e quindi verrà eseguito il training e la pubblicazione dell'app.

## <a name="prerequisites"></a>Prerequisiti

* Ottenere una [sottoscrizione di Azure](https://azure.microsoft.com/free).
* Completare il precedente [argomento di avvio rapido sul portale](get-started-portal-build-app.md) oppure [scaricare e importare l'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Se si hanno app che precedono l'autenticazione delle risorse di Azure, [eseguire la migrazione a una risorsa di Azure](luis-migration-authoring.md). Alcune pagine del portale hanno un aspetto diverso quando è attiva l'autenticazione della posta elettronica.

## <a name="create-the-endpoint-resource"></a>Creare la risorsa endpoint

La risorsa endpoint di stima viene creata nel portale di Azure. Dovrà essere usata solo per le query di stima dell'endpoint. Non usarla per apportare modifiche all'app.

1. Accedere e creare una risorsa nel [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Configurare la sottoscrizione con le impostazioni seguenti:

   |Impostazione|Valore|Scopo|
   |--|--|--|
   |NOME|`my-luis-resource`|Il nome della risorsa di Azure. Questo nome è necessario quando si assegna la risorsa all'app nel portale LUIS.|
   |Subscription|Sottoscrizione in uso|Selezionare una delle sottoscrizioni associate all'account.|
   |Resource group|`my-resource-group`|Creare un nuovo gruppo di risorse per tutte le risorse dei servizi cognitivi. Quando le risorse non sono più necessarie, è possibile eliminare il gruppo per pulire la sottoscrizione. |
   |Posizione di creazione|**Stati Uniti occidentali**|L'area di Azure per la creazione.|
   |Piano tariffario di creazione|**F0**|Il piano tariffario predefinito per la creazione.|
   |Posizione di runtime|**Stati Uniti occidentali**|L'area di Azure per le query dell'endpoint di previsione.|
   |Piano tariffario per il runtime|**S0**|Questo piano tariffario prevede siti Web con traffico elevato.|
   | | | |


   ![Scelta dell'API di Azure](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Selezionare **Crea** per creare la risorsa di Azure.

   La sezione successiva illustrerà come connettere questa nuova risorsa a un'app LUIS nel portale LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Assegnare la chiave della risorsa all'app LUIS nel portale LUIS

Ogni volta che si crea una nuova risorsa per LUIS, è necessario assegnarla all'app LUIS. Dopo l'assegnazione, non sarà più necessario eseguire questo passaggio a meno che non venga creata una nuova risorsa. Si potrebbe creare una nuova risorsa per espandere le aree dell'app oppure per supportare un numero più elevato di query di stima.

1. Accedere al [portale LUIS](https://preview.luis.ai) e scegliere l'app **myEnglishApp** dall'elenco.

1. Scegliere **Manage** (Gestisci) dal menu in alto a destra e quindi selezionare **Azure Resources** (Risorse di Azure).

1. Per aggiungere LUIS, selezionare **Add prediction resource** (Aggiungi risorsa di stima).

    ![Per aggiungere la risorsa di previsione LUIS, selezionare Aggiungi risorsa di stima](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selezionare il tenant, la sottoscrizione e il nome della risorsa. Selezionare **Assign resource** (Assegnare una risorsa).

   ![Assegnare una risorsa all'app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Completare gli stessi passaggi per aggiungere la chiave di creazione all'app.

1. Trovare la nuova riga nella tabella relativa alla nuova risorsa di previsione e copiare l'URL dell'endpoint. Per ottenere una stima, è corretto inoltrare una richiesta `HTTP GET` all'endpoint dell'API LUIS.

## <a name="train-the-app"></a>Eseguire il training dell'app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Pubblicare l'app nell'endpoint di previsione

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Richiesta dell'endpoint di previsione

Nel portale di anteprima l'elemento `query=` alla fine dell'URL è il punto in cui l'espressione dell'utente viene aggiunta alla richiesta GET. Dopo `query=`, immettere la stessa espressione dell'utente usata alla fine del precedente argomento di avvio rapido:

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

Se si vuole vedere questo stesso livello di informazioni nel riquadro di test, è necessario pubblicare l'app. Dopo la pubblicazione dell'app, selezionare **Compare with published** (Confronta con pubblicata) nel riquadro di test. Selezionare **Show JSON view** (Mostra visualizzazione JSON) nel riquadro di test dell'app pubblicata per vedere lo stesso codice JSON indicato nel passaggio precedente. In questo modo è possibile confrontare le modifiche apportate all'app attualmente in uso con un'app pubblicata nell'endpoint.

[![Confrontare la versione dell'app in fase di modifica con quella pubblicata](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, scegliere **My apps** (Le mie app) dal menu di spostamento superiore. Selezionare dall'elenco la casella di controllo dell'app e quindi selezionare **Delete** (Elimina) sulla barra degli strumenti contestuale sopra l'elenco.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Identificare finalità ed entità comuni](luis-tutorial-prebuilt-intents-entities.md)
