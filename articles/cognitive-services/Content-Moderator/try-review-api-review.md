---
title: Creare revisioni di moderazione con la console dell'API REST-Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare le API di Azure Content Moderator Review per creare recensioni di immagini o testo per la moderazione umana.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757148"
---
# <a name="create-human-reviews-rest"></a>Creare recensioni umane (REST)

[Esamina](./review-api.md#reviews) l'archivio e visualizza il contenuto per i moderatori umani da valutare. Quando un utente completa una revisione, i risultati vengono inviati a un endpoint di callback specificato. Questa guida illustra come configurare le revisioni usando le API REST di revisione tramite la console API. Dopo aver compreso la struttura delle API, è possibile eseguire facilmente il porting di queste chiamate a qualsiasi piattaforma compatibile con REST.

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) content moderator.

## <a name="create-a-review"></a>Creare una revisione

Per creare una verifica, passare alla pagina **[Verifica-crea](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** riferimento all'API e selezionare il pulsante per l'area della chiave (è possibile trovarlo nell'URL dell'endpoint nella pagina **credenziali** dello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/)). Viene avviata la console API, in cui è possibile creare ed eseguire facilmente chiamate API REST.

![Verifica-Ottieni selezione area](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Immettere i parametri delle chiamate REST

Immettere i valori per **TeamName**e **OCP-gestione API-Subscription-Key**:

- **TeamName**: ID del team creato quando si configura l'account [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) (disponibile nel campo **ID** nella schermata delle credenziali dello strumento di verifica).
- **OCP-gestione API-Subscription-Key**: chiave di content moderator. È possibile trovarlo nella scheda **Impostazioni** dello strumento di [Revisione](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Immettere una definizione di Revisione

Modificare la casella del **corpo della richiesta** per immettere la richiesta JSON con i campi seguenti:

- **Metadata**: coppie chiave-valore personalizzate da restituire all'endpoint di callback. Se la chiave è un breve codice definito nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), viene visualizzato come tag.
- **Contenuto**: nel caso di contenuto di immagini e video, si tratta di una stringa URL che punta al contenuto. Per il contenuto di testo, si tratta della stringa di testo effettiva.
- **ContentID**: stringa di identificazione personalizzata. Questa stringa viene trasmessa all'API e restituita tramite il callback. È utile per associare identificatori o metadati interni ai risultati di un processo di moderazione.
- **CallbackEndpoint**: (facoltativo) URL per la ricezione delle informazioni di callback al completamento della verifica.

Il corpo della richiesta predefinito Mostra esempi dei diversi tipi di revisione che è possibile creare:

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
  
Selezionare **Send** (Invia). Se l'operazione ha esito positivo, lo **stato della risposta** è `200 OK` e nella casella **contenuto risposta** viene visualizzato un ID per la revisione. Copiare questo ID da usare nei passaggi seguenti.

![La casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione) visualizza l'ID revisione](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Esaminare la nuova revisione

Nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com)selezionare **verifica**  > **immagine** /**testo** /**video** , a seconda del contenuto usato. Il contenuto caricato dovrebbe essere visualizzato, pronto per la revisione umana.

![Immagine di un pallone da calcio nello strumento di revisione](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Ottenere i dettagli della revisione

Per recuperare i dettagli relativi a una revisione esistente, passare alla pagina di riferimento per la [Verifica](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) dell'API e selezionare il pulsante per l'area geografica (area in cui viene amministrata la chiave).

![Selezione dell'area in Workflow - Get (Flusso di lavoro - Acquisizione)](images/test-drive-region.png)

Immettere i parametri di chiamata REST come nella sezione precedente. Per questo passaggio, **reviewId** è la stringa ID univoca ricevuta quando è stata creata la revisione.

![Ottenere i risultati nella console di creazione della revisione](images/test-drive-review-3.PNG)
  
Selezionare **Send** (Invia). Se l'operazione ha esito positivo, lo **stato della risposta** è `200 OK` e nella casella **contenuto risposta** vengono visualizzati i dettagli della verifica in formato JSON, come nel seguente esempio:

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

Prendere nota dei campi seguenti nella risposta:

- **Stato**
- **reviewerResultTags**: viene visualizzato se eventuali tag sono stati aggiunti manualmente dal team di revisione umana (visualizzato il campo **CreatedBy** ).
- **metadati**: Mostra i tag aggiunti inizialmente nella revisione, prima che il team di revisione umana apportasse modifiche.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come creare recensioni di moderazione dei contenuti usando l'API REST. Integrare quindi le revisioni in uno scenario di moderazione end-to-end, ad esempio l'esercitazione relativa alla [moderazione E-commerce](./ecommerce-retail-catalog-moderation.md) .