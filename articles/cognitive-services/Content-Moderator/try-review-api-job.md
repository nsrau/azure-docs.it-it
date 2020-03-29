---
title: Usare i processi di moderazione con la console dell'API REST - Moderatore del contenutoUse moderation jobs with the REST API console - Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare le operazioni per il processo dell'API di revisione per avviare i processi end-to-end di moderazione del contenuto di immagine o testo in Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935962"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definire e utilizzare processi di moderazione (REST)Define and use moderation jobs (REST)

Un processo di moderazione funge da wrapper per la funzionalità di moderazione dei contenuti, flussi di lavoro e revisioni. Questa guida illustra come usare le API REST dei processi per avviare e controllare i processi di moderazione del contenuto. Una volta compresa la struttura delle API, è possibile eseguire facilmente il porting di queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito dello strumento Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)
- (Facoltativo) [Definire un flusso di lavoro personalizzato](./Review-Tool-User-Guide/Workflows.md) da utilizzare con il processo. è inoltre possibile utilizzare il flusso di lavoro predefinito.

## <a name="create-a-job"></a>Creare un processo

Per creare un processo di moderazione, passare alla pagina di riferimento [Processo - Crea](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API e selezionare il pulsante per l'area di sottoscrizione (è possibile trovarlo nell'URL dell'endpoint nella pagina **Credenziali** dello strumento [di revisione).](https://contentmoderator.cognitive.microsoft.com/) Verrà avviata la console API, in cui è possibile creare ed eseguire facilmente chiamate all'API REST.

![Selezione dell'area nella pagina di creazione del processo](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri di chiamata REST

Immettere i valori seguenti per creare la chiamata REST:

- **teamName**: L'ID del team creato durante l'impostazione dell'account [dello strumento](https://contentmoderator.cognitive.microsoft.com/) di revisione (disponibile nel campo **Id** della schermata Credenziali dello strumento di revisione).
- **ContentType**: Può essere "Image", "Text" o "Video".
- **ContentId**: Una stringa di identificazione personalizzata. Questa stringa viene trasmessa all'API e restituita tramite il callback. È utile per associare identificatori interni o metadati ai risultati di un processo di moderazione.
- **Workflowname**: il nome del flusso di lavoro creato in precedenza (o "predefinito" per il flusso di lavoro predefinito).
- **CallbackEndpoint**: (Facoltativo) L'URL per ricevere informazioni di callback al termine della revisione.
- **Ocp-Apim-Subscription-Key:** chiave moderatore del contenuto. È possibile trovarlo nella scheda **Impostazioni** dello [strumento Revisione](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Compilare il corpo della richiesta

Il corpo della chiamata REST contiene un campo, **ContentValue**. Incollare il contenuto di testo non elaborato se si sta moderando il testo o immettere un URL di immagine o video se si sta moderando immagine/video. È possibile utilizzare il seguente URL dell'immagine di esempio:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Parametri di query, intestazioni e casella Corpo della richiesta della console di creazione del processo](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Inviare la richiesta

Selezionare **Invia**. Se l'operazione ha esito `200 OK`positivo, lo stato della **risposta** è e nella casella Contenuto **risposta** viene visualizzato un ID per il processo. Copiare questo ID da usare nei passaggi seguenti.

![La casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione) visualizza l'ID revisione](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Ottenere lo stato di processo

Per ottenere lo stato e i dettagli di un processo in esecuzione o completato, vai alla pagina di riferimento [Processo - Ottieni](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API e seleziona il pulsante per la tua area geografica (l'area in cui viene amministrata la chiave).

![Processo - Ottieni selezione area](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Per questo passaggio, **JobId** è la stringa DI ID univoco ricevuta al momento della creazione del processo. Selezionare **Invia**. Se l'operazione ha esito `200 OK`positivo, lo stato della **risposta** è e la casella Contenuto **risposta** visualizza il processo in formato JSON, come segue:

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

![Processo - Ottenere la risposta alla chiamata REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Esaminare le nuove revisioni

Se il processo di lavoro del contenuto ha determinato la creazione di una revisione, è possibile visualizzarlo nello [strumento Revisione](https://contentmoderator.cognitive.microsoft.com). Seleziona **Rivedi** > **video** di**testo**/**immagine**/(a seconda del contenuto che hai usato). Il contenuto dovrebbe apparire, pronto per la revisione umana. Dopo che un moderatore umano esamina i tag assegnati automaticamente e i dati di stima e invia una decisione di moderazione finale, l'API dei processi invia tutte queste informazioni all'endpoint di callback designato.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come creare ed eseguire query sui processi di moderazione del contenuto usando l'API REST. Successivamente, integrare i processi in uno scenario di moderazione end-to-end, ad esempio l'esercitazione sulla [moderazione dell'e-commerce.](./ecommerce-retail-catalog-moderation.md)