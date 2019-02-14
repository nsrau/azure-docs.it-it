---
title: Eseguire processi di moderazione del contenuto con la console per le API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Usare le operazioni per il processo dell'API di revisione per avviare i processi end-to-end di moderazione del contenuto di immagine o testo in Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9fac80ff152b0f7b9c36c182759d41245364fff9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862429"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Avviare un processo di moderazione dalla console per le API

Usare le [operazioni job](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) dell'API di revisione per avviare i processi end-to-end di moderazione del contenuto di immagine o testo in Azure Content Moderator. 

Il processo di moderazione analizza il contenuto usando l'API Moderazione immagini o l'API Moderazione testo di Content Moderator. Il processo di moderazione usa quindi i flussi di lavoro (definiti nello strumento di revisione) per generare le revisioni nello strumento di revisione. 

Dopo che un moderatore umano ha esaminato i tag assegnati automaticamente e i dati di stima e ha inviato una decisione finale relativa alla moderazione, l'API di revisione invia tutte le informazioni all'endpoint API.

## <a name="prerequisites"></a>Prerequisiti

Passare allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/). Se non è già stato fatto, iscriversi. Nello strumento di revisione [definire un flusso di lavoro personalizzato](Review-Tool-User-Guide/Workflows.md) da usare in questa operazione `Job`.

## <a name="use-the-api-console"></a>Usare la console dell'API
Per eseguire il test drive dell'API usando la console online, sono necessari alcuni valori da immettere nella console:
    
- `teamName`: Usare il campo `Id` della schermata delle credenziali dello strumento di revisione. 
- `ContentId`: Questa stringa viene trasmessa all'API e restituita tramite il callback. **ContentId** è utile per associare i metadati o gli identificatori interni ai risultati di un processo di moderazione.- `Workflowname`: Immettere il nome del [flusso di lavoro creato](Review-Tool-User-Guide/Workflows.md) nella sezione precedente.
- `Ocp-Apim-Subscription-Key`: Disponibile nella scheda **Impostazioni**. Per altre informazioni, vedere la [panoramica](overview.md).

Accedere alla console per le API dalla finestra **Credenziali**.

### <a name="navigate-to-the-api-reference"></a>Passare al riferimento API
Nella finestra **Credentials** (Credenziali) selezionare [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) (Informazioni di riferimento per l'API).

  Viene aperta la pagina `Job.Create`.

### <a name="select-your-region"></a>Selezionare l'area
Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione.
  ![Selezione dell'area nella pagina di creazione del processo](images/test-drive-job-1.png)

  Viene aperta la console dell'API `Job.Create`. 

### <a name="enter-parameters"></a>Immettere i parametri

Immettere i valori per i parametri di query obbligatori e la chiave di sottoscrizione. Nella casella **Corpo della richiesta** specificare la posizione delle informazioni da analizzare. Per questo esempio verrà usata questa [immagine di esempio](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Parametri di query, intestazioni e casella Corpo della richiesta della console di creazione del processo](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Inviare la richiesta
Selezionare **Send** (Invia). Viene creato un ID processo. Copiarlo per usarlo nei passaggi successivi.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Aprire la pagina Get Job details (Ottieni dettagli del processo)
Selezionare **Get** (Ottieni) e quindi aprire l'API selezionando il pulsante corrispondente all'area.

  ![Ottenere i risultati nella console di creazione del processo](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Esaminare la risposta

Immettere i valori per **teamName** e **JobID**. Immettere la chiave di sottoscrizione e quindi selezionare **Send** (Invia). La risposta seguente illustra i dettagli e lo stato del processo.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>Passare allo strumento di revisione
Nel dashboard di Content Moderator selezionare **Review** (Revisione) > **Image** (Immagine). Viene visualizzata l'immagine analizzata, pronta per la revisione umana.

  ![Immagine di strumento di revisione con tre ciclisti](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Passaggi successivi

Usare l'API REST nel codice o seguire la [guida introduttiva a .NET per i processi](moderation-jobs-quickstart-dotnet.md) per procedere all'integrazione con la propria applicazione.
