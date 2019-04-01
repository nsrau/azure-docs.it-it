---
title: Usare i processi di moderazione con la console di API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Usare le operazioni per il processo dell'API di revisione per avviare i processi end-to-end di moderazione del contenuto di immagine o testo in Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756085"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definire e usare i processi di moderazione (REST)

Un processo di moderazione funziona come una specie di wrapper per la funzionalità di moderazione dei contenuti, i flussi di lavoro e le revisioni. Questa guida illustra come usare le API REST del processo per avviare e controllare i processi di moderazione dei contenuti. Dopo aver appreso la struttura delle API, è possibile trasferire facilmente queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedi o crea un account su Content Moderator [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) sito.
- (Facoltativo) [Definire un flusso di lavoro personalizzato](./Review-Tool-User-Guide/Workflows.md) da usare con il processo; è anche possibile usare il flusso di lavoro predefinita.

## <a name="create-a-job"></a>Creare un processo

Per creare un processo di moderazione, passare al [processo: creare](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API pagina di riferimento e selezionare il pulsante per la propria chiave area (è possibile trovarlo nell'URL dell'Endpoint sul **credenziali** pagina del [revisione strumento](https://contentmoderator.cognitive.microsoft.com/)). Verrà avviata la console API, in cui è possibile facilmente costruire ed eseguire chiamate all'API REST.

![Processo - creare la selezione dell'area pagina](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri di chiamata REST

Immettere i valori seguenti per costruire la chiamata REST:

- **teamName**: L'ID team creato quando si configura il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) account (trovato nel **Id** campo nella schermata di credenziali dello strumento di revisione).
- **ContentType**: Può trattarsi di "Image", "Text" o "Video".
- **ContentId**: Una stringa identificatore personalizzato. Questa stringa viene trasmessa all'API e restituita tramite il callback. È utile per l'associazione di identificatori interni o metadati con i risultati di un processo di moderazione.
- **workflowname**: Il nome del flusso di lavoro creato in precedenza (o "default" per il flusso di lavoro predefinito).
- **CallbackEndpoint**: (Facoltativo) L'URL per ricevere informazioni relative al callback quando viene completata la revisione.
- **Ocp-Apim-Subscription-Key**: La chiave di Content Moderator. È possibile trovarlo nel **le impostazioni** scheda della finestra di [strumento di revisione](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Inserire il corpo della richiesta

Il corpo della chiamata REST contiene un campo, **ContentValue**. Incollare il contenuto di testo non elaborato se si è moderazione di testo o immettere un URL video o immagine, se si sta moderazione di immagini e video. È possibile usare l'URL dell'immagine di esempio seguente: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Parametri di query, intestazioni e casella Corpo della richiesta della console di creazione del processo](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Inviare la richiesta

Selezionare **Send** (Invia). Se l'operazione ha esito positivo, il **stato della risposta** viene `200 OK`e il **contenuto della risposta** casella viene visualizzato un ID per il processo. Copiare questo ID da usare nei passaggi seguenti.

![La casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione) visualizza l'ID revisione](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Ottenere lo stato di processo

Per ottenere lo stato e i dettagli di un processo in esecuzione o completato, passare al [processo: ottenere](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API pagina di riferimento e selezionare il pulsante per la propria area (l'area in cui la chiave viene amministrata).

![Processo - selezione dell'area Get](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Per questo passaggio **JobId** stringa ID univoca ricevuto al momento della creazione del processo. Selezionare **Send** (Invia). Se l'operazione ha esito positivo, il **stato della risposta** viene `200 OK`e il **contenuto della risposta** il processo viene visualizzata la finestra in formato JSON, simile al seguente:

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

![Processo: ottenere risposta chiamata REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Esaminare il nuovo review(s)

Se il processo contenuto ha comportato la creazione di una revisione, è possibile visualizzarlo nel [strumento di revisione](https://contentmoderator.cognitive.microsoft.com). Selezionare **revisione** > **immagine**/**testo**/**Video** (a seconda di ciò che il contenuto usato). Il contenuto deve essere visualizzato, pronto per la revisione umana. Dopo la moderazione umana esamina i tag assegnati automaticamente e i dati di stima e invia una decisione finale moderazione, API di processi invia tutte queste informazioni per l'endpoint di endpoint di callback designato.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato descritto come creare ed eseguire query sui processi di moderazione dei contenuti tramite l'API REST. Successivamente, integrare i processi di uno scenario end-to-end moderazione, ad esempio la [moderazione di E-commerce](./ecommerce-retail-catalog-moderation.md) esercitazione.