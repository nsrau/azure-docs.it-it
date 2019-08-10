---
title: Definire i flussi di lavoro di moderazione con la console dell'API REST-Content Moderator
titleSuffix: Azure Cognitive Services
description: È possibile usare le API di Azure Content Moderator Review per definire i flussi di lavoro e le soglie personalizzati in base ai criteri di contenuto.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: cb93cf1b7a5338058c6f2d93b4bb27f60286882e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881198"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definire e usare flussi di lavoro di moderazione (REST)

I flussi di lavoro sono filtri personalizzati basati sul cloud che è possibile usare per gestire il contenuto in modo più efficiente. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Questa guida illustra come usare le API REST del flusso di lavoro, tramite la console API, per creare e usare i flussi di lavoro. Dopo aver compreso la struttura delle API, è possibile eseguire facilmente il porting di queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) content moderator.

## <a name="create-a-workflow"></a>Creare un flusso di lavoro

Per creare o aggiornare un flusso di lavoro, passare alla pagina **[flusso di lavoro-crea o aggiorna](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** riferimento all'API e selezionare il pulsante per l'area della chiave (è possibile trovarlo nell'URL dell'endpoint nella pagina **credenziali** dello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/)). Viene avviata la console API, in cui è possibile creare ed eseguire facilmente chiamate API REST.

![Selezione dell'area nella pagina Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri delle chiamate REST

Immettere i valori per **Team**, **WorkflowName**e **OCP-gestione API-Subscription-Key**:

- **team**: ID del team creato quando si configura l'account [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) (disponibile nel campo **ID** nella schermata delle credenziali dello strumento di verifica).
- **workflowname**: Nome di un nuovo flusso di lavoro da aggiungere o un nome esistente, se si desidera aggiornare un flusso di lavoro esistente.
- **Ocp-Apim-Subscription-Key**: Chiave Content Moderator. È possibile trovarlo nella scheda **Impostazioni** dello strumento di [Revisione](https://contentmoderator.cognitive.microsoft.com).

![Parametri di query e intestazioni nella console Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Immettere una definizione del flusso di lavoro

1. Modificare la casella del **corpo della richiesta** per immettere la richiesta JSON con i dettagli per la **Descrizione** e `Text`il **tipo** ( `Image` o).
2. Per **espressione**copiare l'espressione JSON del flusso di lavoro predefinita. La stringa JSON finale dovrebbe essere simile alla seguente:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> È possibile definire espressioni semplici, complesse e persino nidificate per i flussi di lavoro tramite questa API. La documentazione relativa alla [creazione o all'aggiornamento del flusso di lavoro](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) include esempi di logica più complessa.

### <a name="submit-your-request"></a>Inviare la richiesta
  
Selezionare **Send** (Invia). Se l'operazione viene completata correttamente, lo **stato della risposta** è `200 OK` e la casella **Response content** (Contenuto della risposta) visualizza `true`.

### <a name="examine-the-new-workflow"></a>Esaminare il nuovo flusso di lavoro

Nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/)selezionare **Impostazioni** > flussi di**lavoro**. Il nuovo flusso di lavoro dovrebbe essere visualizzato nell'elenco.

![Esaminare l'elenco di strumenti dei flussi di lavoro](images/workflow-console-new-workflow.PNG)

Selezionare l'opzione **modifica** per il flusso di lavoro e passare alla scheda **finestra di progettazione** . Qui è possibile visualizzare una rappresentazione intuitiva della logica JSON.

![Scheda Designer (Finestra di progettazione) per un flusso di lavoro selezionato](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Ottenere i dettagli del flusso di lavoro

Per recuperare i dettagli relativi a un flusso di lavoro esistente, passare alla pagina di riferimento per l'API **[Workflow-Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** e selezionare il pulsante per l'area geografica (area in cui viene amministrata la chiave).

![Selezione dell'area in Workflow - Get (Flusso di lavoro - Acquisizione)](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Verificare che questa volta WorkflowName sia il nome di un flusso di lavoro esistente.

![Ottenere parametri di query e intestazioni](images/workflow-get-default.PNG)

Selezionare **Send** (Invia). Se l'operazione ha esito positivo, lo stato `200 OK`della **risposta** è e nella casella **contenuto risposta** viene visualizzato il flusso di lavoro in formato JSON, come indicato di seguito:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare i flussi di lavoro con i [processi di moderazione del contenuto](try-review-api-job.md).