---
title: Creare revisioni della moderazione con la console dell'API REST - Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare le API di revisione di Azure Content Moderator per creare revisioni di immagini o di testo per la moderazione umana.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757148"
---
# <a name="create-human-reviews-rest"></a>Creare revisioni umane (REST)Create human reviews (REST)

[Recensioni](./review-api.md#reviews) memorizzare e visualizzare i contenuti per i moderatori umani da valutare. Quando un utente completa una revisione, i risultati vengono inviati a un endpoint di callback specificato. In questa guida imparerai a configurare le recensioni usando le API REST delle recensioni tramite la console API. Una volta compresa la struttura delle API, è possibile eseguire facilmente il porting di queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito dello strumento Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-review"></a>Creare una revisione

Per creare una revisione, passare alla pagina di riferimento **[Revisione - Crea](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API e selezionare il pulsante per l'area chiave (è possibile trovarlo nell'URL dell'endpoint nella pagina **Credenziali** dello strumento di [revisione).](https://contentmoderator.cognitive.microsoft.com/) Verrà avviata la console API, in cui è possibile creare ed eseguire facilmente chiamate all'API REST.

![Revisione - Ottenere la selezione dell'areaReview - Get region selection](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri di chiamata REST

Immettere i valori per **teamName**e **Ocp-Apim-Subscription-Key**:

- **teamName**: L'ID del team creato durante l'impostazione dell'account [dello strumento](https://contentmoderator.cognitive.microsoft.com/) di revisione (disponibile nel campo **Id** della schermata Credenziali dello strumento di revisione).
- **Ocp-Apim-Subscription-Key:** chiave moderatore del contenuto. È possibile trovarlo nella scheda **Impostazioni** dello [strumento Revisione](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Immettere una definizione di revisione

Modificare la casella **Corpo richiesta** per immettere la richiesta JSON con i campi seguenti:

- **Metadati:** coppie chiave-valore personalizzate da restituire all'endpoint di callback. Se la chiave è un codice breve definito nello [strumento Revisione](https://contentmoderator.cognitive.microsoft.com), viene visualizzato come tag.
- **Contenuto**: Nel caso di contenuti Image e Video, si tratta di una stringa URL che punta al contenuto. Per il contenuto di testo, questa è la stringa di testo effettiva.
- **ContentId**: Una stringa di identificazione personalizzata. Questa stringa viene trasmessa all'API e restituita tramite il callback. È utile per associare identificatori interni o metadati ai risultati di un processo di moderazione.
- **CallbackEndpoint**: (Facoltativo) L'URL per ricevere informazioni di callback al termine della revisione.

Il corpo della richiesta predefinito mostra esempi dei diversi tipi di recensioni che è possibile creare:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Inviare la richiesta
  
Selezionare **Invia**. Se l'operazione ha esito `200 OK`positivo, lo stato della **risposta** è e nella casella Contenuto **risposta** viene visualizzato un ID per la revisione. Copiare questo ID da usare nei passaggi seguenti.

![La casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione) visualizza l'ID revisione](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Esaminare la nuova revisione

Nello [strumento Revisione,](https://contentmoderator.cognitive.microsoft.com)selezionate **Rivedi** > **video** di**testo**/**immagine**/(a seconda del contenuto utilizzato). Il contenuto che hai caricato dovrebbe essere visualizzato, pronto per la revisione umana.

![Immagine di un pallone da calcio nello strumento di revisione](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Ottenere i dettagli della revisione

Per recuperare i dettagli relativi a una revisione esistente, vai alla pagina di riferimento [Rivedi - Ottieni](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API e seleziona il pulsante per la tua area geografica (l'area in cui viene amministrata la chiave).

![Selezione dell'area in Workflow - Get (Flusso di lavoro - Acquisizione)](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Per questo passaggio, **reviewId** è la stringa di ID univoco ricevuta al momento della creazione della revisione.

![Ottenere i risultati nella console di creazione della revisione](images/test-drive-review-3.PNG)
  
Selezionare **Invia**. Se l'operazione ha esito `200 OK`positivo, lo stato della **risposta** è e nella casella Contenuto **risposta** i dettagli della revisione vengono visualizzati in formato JSON, come segue:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Prendere nota dei seguenti campi nella risposta:

- **status**
- **reviewerResultTags**: Viene visualizzato se i tag sono stati aggiunti manualmente dal team di revisione umana (mostrato il campo **createdBy).**
- **metadata**: Mostra i tag che sono stati inizialmente aggiunti nella recensione, prima che il team di revisione umana apporti modifiche.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come creare revisioni della moderazione del contenuto usando l'API REST. Successivamente, integrare le revisioni in uno scenario di moderazione end-to-end, ad esempio l'esercitazione sulla [moderazione dell'e-commerce.](./ecommerce-retail-catalog-moderation.md)