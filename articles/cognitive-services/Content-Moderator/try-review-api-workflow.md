---
title: Definire i flussi di lavoro di moderazione con la console di API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: È possibile utilizzare le API di Azure contenuto Moderator revisione per definire i flussi di lavoro personalizzati e le soglie in base a criteri di contenuto.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605843"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definire e usare i flussi di lavoro di moderazione (REST)

I flussi di lavoro sono filtri personalizzati basati sul cloud che è possibile usare per gestire il contenuto in modo più efficiente. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Questa guida illustra come usare il flusso di lavoro le API REST, tramite la console API, creare e usare i flussi di lavoro. Dopo aver appreso la struttura delle API, è possibile trasferire facilmente queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedi o crea un account su Content Moderator [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) sito.

## <a name="create-a-workflow"></a>Creare un flusso di lavoro

Per creare o aggiornare un flusso di lavoro, passare al **[flusso di lavoro - creare o aggiornare](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API pagina di riferimento e selezionare il pulsante per la propria chiave area (è possibile trovarlo nell'URL dell'Endpoint sul **credenziali**  della pagina la [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/)). Verrà avviata la console API, in cui è possibile facilmente costruire ed eseguire chiamate all'API REST.

![Selezione dell'area nella pagina Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri di chiamata REST

Immettere i valori relativi **team**, **workflowname**, e **Ocp-Apim-Subscription-Key**:

- **team**: L'ID team creato quando si configura il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) account (trovato nel **Id** campo nella schermata di credenziali dello strumento di revisione).
- **workflowname**: Il nome di un nuovo flusso di lavoro da aggiungere (o un nome esistente, se si desidera aggiornare un flusso di lavoro esistente).
- **Ocp-Apim-Subscription-Key**: La chiave di Content Moderator. È possibile trovarlo nel **le impostazioni** scheda della finestra di [strumento di revisione](https://contentmoderator.cognitive.microsoft.com).

![Parametri di query e intestazioni nella console Workflow - Create Or Update (Flusso di lavoro - Creazione o aggiornamento)](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Immettere una definizione del flusso di lavoro

1. Modificare il **corpo della richiesta** finestra di immettere la richiesta JSON con i dettagli per **descrizione** e **tipo** (sia `Image` o `Text`).
2. Per la **espressione**, copiare l'espressione JSON del flusso di lavoro. La stringa JSON finale dovrebbe essere simile al seguente:

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
> È possibile definire le espressioni semplici, complesse e persino annidate per flussi di lavoro usando questa API. Il [flusso di lavoro - creare o aggiornare](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) documentazione contiene esempi di logica più complessa.

### <a name="submit-your-request"></a>Inviare la richiesta
  
Selezionare **Send** (Invia). Se l'operazione viene completata correttamente, lo **stato della risposta** è `200 OK` e la casella **Response content** (Contenuto della risposta) visualizza `true`.

### <a name="examine-the-new-workflow"></a>Esaminare il nuovo flusso di lavoro

Nel [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/), selezionare **impostazioni** > **i flussi di lavoro**. Il nuovo flusso di lavoro dovrebbe essere visualizzato nell'elenco.

![Esaminare l'elenco di strumenti dei flussi di lavoro](images/workflow-console-new-workflow.PNG)

Selezionare il **Edit** l'opzione del flusso di lavoro e passare al **progettazione** scheda. In questo caso, è possibile visualizzare una rappresentazione intuitiva della logica di JSON.

![Scheda Designer (Finestra di progettazione) per un flusso di lavoro selezionato](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Ottenere i dettagli del flusso di lavoro

Per recuperare informazioni dettagliate su un flusso di lavoro esistente, passare al **[flusso di lavoro - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API pagina di riferimento e selezionare il pulsante per la propria area (l'area in cui la chiave viene amministrata).

![Selezione dell'area in Workflow - Get (Flusso di lavoro - Acquisizione)](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Assicurarsi che questa volta **workflowname** è il nome di un flusso di lavoro esistente.

![Ottenere parametri di query e intestazioni](images/workflow-get-default.PNG)

Selezionare **Send** (Invia). Se l'operazione ha esito positivo, il **stato della risposta** viene `200 OK`e il **contenuto della risposta** il flusso di lavoro viene visualizzata la finestra in formato JSON, simile al seguente:

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