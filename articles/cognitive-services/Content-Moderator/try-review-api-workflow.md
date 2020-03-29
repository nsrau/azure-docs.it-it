---
title: Definire flussi di lavoro di moderazione con la console dell'API REST - Content Moderator
titleSuffix: Azure Cognitive Services
description: È possibile usare le API di revisione di Azure Content Moderator per definire flussi di lavoro e soglie personalizzati in base ai criteri del contenuto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754174"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definire e utilizzare flussi di lavoro di moderazione (REST)Define and use moderation workflows (REST)

I flussi di lavoro sono filtri personalizzati basati su cloud che è possibile utilizzare per gestire i contenuti in modo più efficiente. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Questa guida illustra come usare le API REST del flusso di lavoro, tramite la console API, per creare e usare flussi di lavoro. Una volta compresa la struttura delle API, è possibile eseguire facilmente il porting di queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito dello strumento Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-workflow"></a>Creare un flusso di lavoro

Per creare o aggiornare un flusso di lavoro, passare alla pagina di riferimento **[Flusso di lavoro - Crea o aggiorna](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API e selezionare il pulsante per l'area chiave (è possibile trovarlo nell'URL dell'endpoint nella pagina **Credenziali** dello strumento [di revisione).](https://contentmoderator.cognitive.microsoft.com/) Verrà avviata la console API, in cui è possibile creare ed eseguire facilmente chiamate all'API REST.

![Selezione dell'area nella pagina Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri di chiamata REST

Immettere i valori per **team**, **nomeflusso di lavoro**e **Ocp-Apim-Subscription-Key**:

- **team**: l'ID del team che hai creato quando hai configurato il tuo account [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) (disponibile nel campo **Id** della schermata Credenziali dello strumento di revisione).
- **workflowname**: Il nome di un nuovo flusso di lavoro da aggiungere (o un nome esistente, se si desidera aggiornare un flusso di lavoro esistente).
- **Ocp-Apim-Subscription-Key:** chiave moderatore del contenuto. È possibile trovarlo nella scheda **Impostazioni** dello [strumento Revisione](https://contentmoderator.cognitive.microsoft.com).

![Parametri di query e intestazioni nella console Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Immettere una definizione del flusso di lavoro

1. Modificare la casella **Corpo richiesta** per immettere la richiesta JSON con i dettagli relativi **a Descrizione** e **Tipo** (o `Image` o `Text`).
2. Per **Expression**, copiare l'espressione JSON del flusso di lavoro predefinita. La stringa JSON finale dovrebbe essere simile alla seguente:Your final JSON string should look like this:

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
> È possibile definire espressioni semplici, complesse e persino annidate per i flussi di lavoro usando questa API. La documentazione [Flusso di lavoro - Crea o Aggiorna](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) contiene esempi di logica più complessa.

### <a name="submit-your-request"></a>Inviare la richiesta
  
Selezionare **Invia**. Se l'operazione viene completata correttamente, lo **stato della risposta** è `200 OK` e la casella **Response content** (Contenuto della risposta) visualizza `true`.

### <a name="examine-the-new-workflow"></a>Esaminare il nuovo flusso di lavoro

Nello [strumento Revisione](https://contentmoderator.cognitive.microsoft.com/)selezionare**Flussi di lavoro** **impostazioni** > . Il nuovo flusso di lavoro dovrebbe essere visualizzato nell'elenco.

![Esaminare l'elenco di strumenti dei flussi di lavoro](images/workflow-console-new-workflow.PNG)

Selezionare l'opzione **Modifica** per il flusso di lavoro e passare alla scheda **Progettazione.** In questo caso, è possibile visualizzare una rappresentazione intuitiva della logica JSON.

![Scheda Designer (Finestra di progettazione) per un flusso di lavoro selezionato](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Ottenere i dettagli del flusso di lavoro

Per recuperare i dettagli su un flusso di lavoro esistente, passare alla pagina di riferimento **[Flusso di lavoro - Ottieni](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API e selezionare il pulsante per l'area geografica (l'area in cui viene amministrata la chiave).

![Selezione dell'area in Workflow - Get (Flusso di lavoro - Acquisizione)](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Assicurarsi che questa volta, **workflowname** sia il nome di un flusso di lavoro esistente.

![Ottenere parametri di query e intestazioni](images/workflow-get-default.PNG)

Selezionare **Invia**. Se l'operazione ha esito `200 OK`positivo, lo stato della **risposta** è e la casella Contenuto **risposta** visualizza il flusso di lavoro in formato JSON, come segue:

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