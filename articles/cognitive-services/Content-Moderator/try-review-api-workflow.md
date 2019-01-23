---
title: Flussi di lavoro di moderazione del contenuto dalla console per le API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Usare le operazioni di flusso di lavoro in Azure Content Moderator per creare o aggiornare un flusso di lavoro oppure ottenere i dettagli del flusso di lavoro tramite l'API di revisione.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 24c451dda398cfa65dd24a4500871c67943b79bf
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259460"
---
# <a name="workflows-from-the-api-console"></a>Flussi di lavoro dalla console per le API

Usare le [operazioni workflow](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) in Azure Content Moderator per creare o aggiornare un flusso di lavoro o ottenere i dettagli del flusso di lavoro usando l'API di revisione. Usando questa API è possibile definire espressioni semplici, complesse e anche annidate. I flussi di lavoro vengono visualizzati nello strumento di revisione per consentire al team di usarli. I flussi di lavoro vengono usati anche dalle operazioni Job dell'API di revisione.

## <a name="prerequisites"></a>Prerequisiti

1. Andare allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/). Se non è già stato fatto, iscriversi. 
2. Nello strumento di revisione, in **Settings** (Impostazioni) selezionare la scheda **Workflows** (Flussi di lavoro), come illustrato nell'[esercitazione sui flussi di lavoro](Review-Tool-User-Guide/Workflows.md) dello strumento di revisione.

### <a name="browse-to-the-workflows-screen"></a>Passare alla schermata Workflows (Flussi di lavoro)

Nel dashboard di Content Moderator selezionare **Review** (Revisione) > **Settings** (Impostazioni) > **Workflows** (Flussi di lavoro). Viene visualizzato un flusso di lavoro predefinito.

  ![Flusso di lavoro predefinito](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Ottenere la definizione JSON del flusso di lavoro predefinito

Selezionare l'opzione **Edit** (Modifica) per il flusso di lavoro e quindi selezionare la scheda **JSON**. Viene visualizzata l'espressione JSON seguente:

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>Ottenere i dettagli del flusso di lavoro

Usare l'operazione **Workflow - Get** (Flusso di lavoro - Acquisizione) per ottenere i dettagli del flusso di lavoro predefinito esistente.

Nello strumento di revisione andare alla sezione [Credentials](Review-Tool-User-Guide/credentials.md#the-review-tool) (Credenziali).

### <a name="browse-to-the-api-reference"></a>Passare al riferimento API

1. Nella visualizzazione **Credentials** (Credenziali) selezionare [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) (Informazioni di riferimento per l'API). 
2. Quando si apre la pagina **Workflow - Create Or Update** (Flusso di lavoro - Creazione o aggiornamento), andare alle informazioni di riferimento [Workflow - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) (Flusso di lavoro - Acquisizione).

### <a name="select-your-region"></a>Selezionare l'area

Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione.

  ![Selezione dell'area in Workflow - Get (Flusso di lavoro - Acquisizione)](images/test-drive-region.png)

  Viene aperta la console dell'API **Workflow - Get** (Flusso di lavoro - Acquisizione).

### <a name="enter-parameters"></a>Immettere i parametri

Immettere i valori per **team**, **workflowname** e **Ocp-Apim-Subscription-Key** (la chiave di sottoscrizione):

- **team**: l'ID team creato quando è stato configurato l'[account dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: il nome del flusso di lavoro. Usare `default`.
- **Ocp-Apim-Subscription-Key**: disponibile nella scheda **Impostazioni**. Per altre informazioni, vedere la [panoramica](overview.md).

  ![Ottenere parametri di query e intestazioni](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Inviare la richiesta
  
Selezionare **Send** (Invia). Se l'operazione viene completata correttamente, lo **stato della risposta** è `200 OK` e la casella **Response content** (Contenuto della risposta) visualizza il flusso di lavoro JSON seguente:

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>Creare un flusso di lavoro

Nello strumento di revisione andare alla sezione [Credentials](Review-Tool-User-Guide/credentials.md#the-review-tool) (Credenziali).

### <a name="browse-to-the-api-reference"></a>Passare al riferimento API

Nella visualizzazione **Credentials** (Credenziali) selezionare [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) (Informazioni di riferimento per l'API). Viene aperta la pagina **Workflow - Create Or Update** (Flusso di lavoro - Creazione o aggiornamento).

### <a name="select-your-region"></a>Selezionare l'area

Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione.

  ![Selezione dell'area nella pagina Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/test-drive-region.png)

  Viene aperta la console dell'API **Workflow - Create Or Update** (Flusso di lavoro - Creazione o aggiornamento).

### <a name="enter-parameters"></a>Immettere i parametri

Immettere i valori per **team**, **workflowname** e **Ocp-Apim-Subscription-Key** (la chiave di sottoscrizione):

- **team**: l'ID team creato quando è stato configurato l'[account dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: il nome del nuovo flusso di lavoro.
- **Ocp-Apim-Subscription-Key**: disponibile nella scheda **Impostazioni**. Per altre informazioni, vedere la [panoramica](overview.md).

  ![Parametri di query e intestazioni nella console Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Immettere la definizione del flusso di lavoro

1. Modificare la casella **Request body** (Corpo della richiesta) per immettere la richiesta JSON con i dettagli per **Description** e **Type** (Image o Text). 
2. Per **Expression**, copiare l'espressione del flusso di lavoro predefinito della sezione precedente, come illustrato qui:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    Il corpo della richiesta sarà simile alla richiesta JSON seguente:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>Inviare la richiesta
  
Selezionare **Send** (Invia). Se l'operazione viene completata correttamente, lo **stato della risposta** è `200 OK` e la casella **Response content** (Contenuto della risposta) visualizza `true`.

### <a name="check-out-the-new-workflow"></a>Controllare il nuovo flusso di lavoro

Nello strumento di revisione selezionare**Review** (Revisione) > **Settings** (Impostazioni) > **Workflows** (Flussi di lavoro). Il nuovo flusso di lavoro viene visualizzato ed è pronto per essere usato.

  ![Esaminare l'elenco di strumenti dei flussi di lavoro](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Esaminare i dettagli del nuovo flusso di lavoro

1. Selezionare l'opzione **Edit** (Modifica) per il flusso di lavoro e quindi selezionare le schede **Designer** (Finestra di progettazione) e **JSON**.

   ![Scheda Designer (Finestra di progettazione) per un flusso di lavoro selezionato](images/workflow-console-new-workflow-designer.PNG)

2. Per vedere la visualizzazione JSON del flusso di lavoro, selezionare la scheda **JSON**.

## <a name="next-steps"></a>Passaggi successivi

* Per esempi di flussi di lavoro più complessi, vedere la [panoramica dei flussi di lavoro](workflow-api.md).
* Informazioni su come usare i flussi di lavoro con i [processi di moderazione del contenuto](try-review-api-job.md).
