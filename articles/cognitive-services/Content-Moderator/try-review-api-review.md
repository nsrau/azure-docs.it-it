---
title: Creare le verifiche di moderazione con la console di API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Usare l'API Azure Content Moderator revisione per creare l'immagine o le revisioni del testo per la moderazione umana.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607515"
---
# <a name="create-human-reviews-rest"></a>Creazione di revisione umana (REST)

[Le verifiche](./review-api.md#reviews) archiviare e visualizzare il contenuto da moderatori umani per valutare. Quando un utente ha completato un'analisi, i risultati vengono inviati a un endpoint di callback specificati. In questa guida si apprenderà come configurare le verifiche tramite la revisione API REST tramite la console API. Dopo aver appreso la struttura delle API, è possibile trasferire facilmente queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedi o crea un account su Content Moderator [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) sito.

## <a name="create-a-review"></a>Scrivere una recensione

Per creare una revisione, passare al **[rivedere - creare](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API pagina di riferimento e selezionare il pulsante per la propria chiave area (è possibile trovarlo nell'URL dell'Endpoint sul **credenziali** pagina del [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/)). Verrà avviata la console API, in cui è possibile facilmente costruire ed eseguire chiamate all'API REST.

![Esaminare: selezione area Get](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri di chiamata REST

Immettere i valori relativi **teamName**, e **Ocp-Apim-Subscription-Key**:

- **teamName**: L'ID team creato quando si configura il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) account (trovato nel **Id** campo nella schermata di credenziali dello strumento di revisione).
- **Ocp-Apim-Subscription-Key**: La chiave di Content Moderator. È possibile trovarlo nel **le impostazioni** scheda della finestra di [strumento di revisione](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Immettere una definizione di revisione

Modificare il **corpo della richiesta** casella immettere la richiesta JSON con i campi seguenti:

- **Metadati**: Coppie chiave-valore personalizzate da restituire all'endpoint di callback. Se la chiave è un codice breve è definito nel [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), viene visualizzato come un tag.
- **Contenuto**: Nel caso di contenuti immagine e Video, questa è una stringa URL che punta al contenuto. Per il contenuto di testo, si tratta della stringa di testo effettivo.
- **ContentId**: Una stringa identificatore personalizzato. Questa stringa viene trasmessa all'API e restituita tramite il callback. È utile per l'associazione di identificatori interni o metadati con i risultati di un processo di moderazione.
- **CallbackEndpoint**: (Facoltativo) L'URL per ricevere informazioni relative al callback quando viene completata la revisione.

Il corpo della richiesta di impostazione predefinita vengono illustrati esempi dei diversi tipi di recensioni, che è possibile creare:

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
  
Selezionare **Send** (Invia). Se l'operazione ha esito positivo, il **stato della risposta** viene `200 OK`e il **contenuto della risposta** casella viene visualizzato un ID per la revisione. Copiare questo ID da usare nei passaggi seguenti.

![La casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione) visualizza l'ID revisione](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Esaminare la nuova verifica

Nel [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), selezionare **revisione** > **immagine**/**Text** / **Video** (a seconda del contenuto che è usato). Il contenuto caricato deve essere visualizzato, pronto per la revisione umana.

![Immagine di un pallone da calcio nello strumento di revisione](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Ottenere i dettagli di revisione

Per recuperare informazioni dettagliate su una verifica di esistente, passare al [esaminare - Get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API pagina di riferimento e selezionare il pulsante per la propria area (l'area in cui la chiave viene amministrata).

![Selezione dell'area in Workflow - Get (Flusso di lavoro - Acquisizione)](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Per questo passaggio **reviewId** stringa ID univoca ricevuto al momento della creazione della revisione.

![Ottenere i risultati nella console di creazione della revisione](images/test-drive-review-3.PNG)
  
Selezionare **Send** (Invia). Se l'operazione ha esito positivo, il **stato della risposta** viene `200 OK`e il **contenuto della risposta** casella vengono visualizzati i dettagli di revisione in formato JSON, simile al seguente:

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

- **Stato**
- **reviewerResultTags**: Viene visualizzato se eventuali tag sono stati aggiunti manualmente dal team di revisione umana (illustrato il **createdBy** campo).
- **metadata**: Vengono visualizzati i tag che sono stati aggiunti inizialmente durante la verifica prima che le modifiche apportate team di revisione umana.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato descritto come creare le verifiche di moderazione dei contenuti tramite l'API REST. Successivamente, integrare le verifiche di uno scenario end-to-end moderazione, ad esempio la [moderazione di E-commerce](./ecommerce-retail-catalog-moderation.md) esercitazione.