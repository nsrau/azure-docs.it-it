---
title: Usare i processi di moderazione con la console dell'API REST-Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare le operazioni per il processo dell'API di revisione per avviare i processi end-to-end di moderazione del contenuto di immagine o testo in Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: c6f3d9c1605dc97b315550d8b7e3fdf08144c1bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561227"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definire e usare processi di moderazione (REST)

Un processo di moderazione funge da wrapper per la funzionalità di moderazione del contenuto, flussi di lavoro e revisioni. Questa guida illustra come usare le API REST del processo per avviare e controllare i processi di moderazione del contenuto. Dopo aver compreso la struttura delle API, è possibile eseguire facilmente il porting di queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) content moderator.
- Opzionale [Definire un flusso](./Review-Tool-User-Guide/Workflows.md) di lavoro personalizzato da usare con il processo. è anche possibile usare il flusso di lavoro predefinito.

## <a name="create-a-job"></a>Crea un processo

Per creare un processo di moderazione, passare alla pagina di riferimento per l'API [Crea processo](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) e selezionare il pulsante per l'area della chiave (è possibile trovarlo nell'URL dell'endpoint nella pagina **credenziali** dello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/)). Viene avviata la console API, in cui è possibile creare ed eseguire facilmente chiamate API REST.

![Processo-Crea selezione area della pagina](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri delle chiamate REST

Immettere i valori seguenti per costruire la chiamata REST:

- **teamName**: ID del team creato quando si configura l'account [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) (disponibile nel campo **ID** nella schermata delle credenziali dello strumento di verifica).
- **ContentType**: Può essere "image", "Text" o "video".
- **ContentId**: Stringa dell'identificatore personalizzato. Questa stringa viene trasmessa all'API e restituita tramite il callback. È utile per associare identificatori o metadati interni ai risultati di un processo di moderazione.
- **WorkflowName**: Nome del flusso di lavoro creato in precedenza (o "predefinito" per il flusso di lavoro predefinito).
- **CallbackEndpoint**: Opzionale URL per la ricezione delle informazioni di callback al completamento della verifica.
- **Ocp-Apim-Subscription-Key**: Chiave Content Moderator. È possibile trovarlo nella scheda **Impostazioni** dello strumento di [Revisione](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Compilare il corpo della richiesta

Il corpo della chiamata REST contiene un campo, **ContentValue**. Incollare il contenuto di testo non elaborato se si sta moderando il testo oppure immettere un'immagine o un URL video se si sta moderando l'immagine o il video. È possibile usare l'URL dell'immagine di esempio seguente:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Parametri di query, intestazioni e casella Corpo della richiesta della console di creazione del processo](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Inviare la richiesta

Selezionare **Send** (Invia). Se l'operazione ha esito positivo, lo stato `200 OK`della **risposta** è e nella casella **contenuto risposta** viene visualizzato un ID per il processo. Copiare questo ID da usare nei passaggi seguenti.

![La casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione) visualizza l'ID revisione](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Ottenere lo stato di processo

Per ottenere lo stato e i dettagli di un processo in esecuzione o completato, passare alla pagina di riferimento per l'API [Job-get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) e selezionare il pulsante per l'area geografica (area in cui viene amministrata la chiave).

![Processo-Ottieni selezione area](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Per questo passaggio, **JobID** è la stringa ID univoca ricevuta al momento della creazione del processo. Selezionare **Send** (Invia). Se l'operazione ha esito positivo, lo stato `200 OK`della **risposta** è e nella casella **contenuto risposta** viene visualizzato il processo in formato JSON, come indicato di seguito:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Processo-Ottieni risposta alla chiamata REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Esaminare le nuove verifiche

Se il processo di contenuto ha comportato la creazione di una revisione, è possibile visualizzarla nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com). Selezionare **Verifica** > il/**video** del**testo**dell'immagine(asecondadelcontenutousato/). Il contenuto dovrebbe essere visualizzato, pronto per la revisione umana. Quando un moderatore umano esamina i tag e i dati di stima assegnati automaticamente e invia una decisione di moderazione finale, l'API Jobs Invia tutte queste informazioni all'endpoint dell'endpoint di callback designato.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come creare ed eseguire query sui processi di moderazione del contenuto usando l'API REST. Integrare quindi i processi in uno scenario di moderazione end-to-end, ad esempio l'esercitazione relativa alla [moderazione E-commerce](./ecommerce-retail-catalog-moderation.md) .