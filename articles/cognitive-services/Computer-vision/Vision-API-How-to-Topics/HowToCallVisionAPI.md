---
title: Chiamare l'API Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni su come chiamare l'API Visione artificiale usando l'API REST in Servizi cognitivi di Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177053"
---
# <a name="call-the-computer-vision-api"></a>Chiamare l'API Visione artificiale

Questo articolo illustra come chiamare l'API Visione artificiale usando l'API REST. Gli esempi sono scritti sia in C# usando la libreria client dell'API Visione artificiale, sia come chiamate HTTP POST o GET. L'articolo descrive come:

- Ottenere tag, una descrizione e le categorie
- Ottenere informazioni specifiche del dominio ("celebrities")

## <a name="prerequisites"></a>Prerequisiti

- Un URL o un percorso di un'immagine archiviata in locale
- Metodi di input supportati: un file binario dell'immagine RAW di tipo application/octet-stream o un URL dell'immagine
- Formati di immagine supportati: JPEG, PNG, GIF e BMP
- Dimensioni file di immagine: massimo 4 MB
- Dimensioni delle immagini: 50 &times; 50 pixel o superiori
  
Gli esempi di questo articolo dimostrano le funzionalità seguenti:

* Analisi di un'immagine per restituire una matrice di tag e una descrizione
* Analisi di un'immagine con un modello specifico del dominio (in particolare, il modello "celebrities") per restituire il risultato corrispondente in formato JSON

Le funzionalità offrono le opzioni seguenti:

- **Opzione 1**: Analisi con ambito - Analisi di un uno solo modello specificato
- **Opzione 2**: Analisi avanzata - Analisi per ottenere dettagli aggiuntivi tramite [86 categorie di tassonomia](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autorizzare la chiamata API

Per ogni chiamata all'API Visione artificiale è richiesta una chiave di sottoscrizione. Questa chiave deve essere passata tramite un parametro di stringa di query o specificata nell'intestazione della richiesta.

Per ottenere una chiave di valutazione gratuita, eseguire una delle operazioni seguenti:
* Accedere alla pagina [Prova dei servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). 
* Passare alla pagina per [creare un account di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) e sottoscrivere Visione artificiale.

Per passare la chiave della sottoscrizione, è possibile eseguire una delle operazioni seguenti:

* Passare la chiave tramite una stringa di query, come in questo esempio dell'API Visione artificiale:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Specificare la chiave nell'intestazione della richiesta HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Se si usa la libreria client, passare la chiave tramite il costruttore di ComputerVisionClient e specificare l'area in una proprietà del client:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Caricare un'immagine nel servizio API Visione artificiale

Il modo più semplice per eseguire la chiamata dell'API Visione artificiale consiste nel caricare direttamente un'immagine per restituire tag, una descrizione e le celebrità. A tale scopo, inviare una richiesta "POST" con l'immagine binaria nel corpo HTTP insieme ai dati letti dall'immagine. Il metodo di caricamento è lo stesso per tutte le chiamate all'API Visione artificiale. L'unica differenza è rappresentata dai parametri di query specificati. 

Per un'immagine specificata, ottenere i tag e una descrizione usando una delle opzioni seguenti:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Opzione 1: Ottenere un elenco di tag e una descrizione

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Opzione 2: Ottenere solo un elenco di tag o solo una descrizione

Per ottenere solo i tag, eseguire:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Per ottenere solo la descrizione, eseguire:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Ottenere l'analisi specifica del dominio (celebrità)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opzione 1: Analisi con ambito - Analisi di un uno solo modello specificato
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Per questa opzione, tutti gli altri parametri di query {visualFeatures, details} non sono validi. Se si vogliono visualizzare tutti i modelli supportati, usare:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Opzione 2: Analisi avanzata - Analisi per ottenere dettagli aggiuntivi tramite 86 categorie di tassonomia

Per le applicazioni in cui si vuole ottenere un'analisi generica delle immagini, oltre ai dettagli di uno o più modelli specifici del dominio, estendere l'API v1 con il parametro di query models.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Quando si richiama questo metodo, si chiama prima il classificatore di [86 categorie](../Category-Taxonomy.md). Se una delle categorie corrisponde a quella di un modello noto/corrispondente, verrà eseguito un secondo passaggio delle chiamate del classificatore. Ad esempio, se "details = all" o "details" include "celebrities", il modello delle celebrità viene chiamato dopo la chiamata al classificatore di 86 categorie. Il risultato include la categoria person. Diversamente dall'opzione 1, questo metodo aumenta la latenza per gli utenti interessati alle celebrità.

In questo caso, il comportamento di tutti i parametri di query v1 sarà identico. Se non si specifica visualFeatures = categories, questa impostazione viene abilitata in modo implicito.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Recuperare e interpretare l'output JSON per l'analisi

Ad esempio:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Campo | Type | Content
------|------|------|
Tag  | `object` | L'oggetto di primo livello per una matrice di tag.
tags[].Name | `string`  | La parola chiave del classificatore di tag.
tags[].Score    | `number`  | Il punteggio di attendibilità, compreso tra 0 e 1.
description  | `object` | L'oggetto di primo livello per una descrizione.
description.tags[] |    `string`    | L'elenco di tag.  Se l'attendibilità nella capacità di produrre una didascalia non è sufficiente, i tag potrebbero essere l'unica informazione disponibile per il chiamante.
description.captions[].text | `string`  | Frase che descrive l'immagine.
description.captions[].confidence   | `number`  | Il punteggio di attendibilità per la frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recuperare e interpretare l'output JSON per i modelli specifici del dominio

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opzione 1: Analisi con ambito - Analisi di un uno solo modello specificato

L'output è una matrice di tag, come illustrato nell'esempio seguente:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Opzione 2: Analisi avanzata - Analisi per ottenere dettagli aggiuntivi tramite 86 categorie di tassonomia

Per i modelli specifici del dominio che usano l'opzione 2 (analisi avanzata), il tipo restituito delle categorie è esteso, come illustrato nell'esempio seguente:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Il campo categories è un elenco di una o più delle [86 categorie](../Category-Taxonomy.md) della tassonomia originale. Le categorie che terminano con un carattere di sottolineatura corrisponderanno alla categoria in oggetto e ai relativi elementi figlio (ad esempio, "people_" o "people_group" per il modello celebrities).

Campo   | Type  | Content
------|------|------|
Categorie | `object`   | L'oggetto di primo livello.
categories[].name    | `string` | Il nome dell'elenco delle 86 categorie di tassonomia.
categories[].score  | `number`  | Il punteggio di attendibilità, compreso tra 0 e 1.
categories[].detail  | `object?`      | (Facoltativo) L'oggetto dettagli.

In caso di corrispondenza di più categorie (ad esempio, il classificatore delle 86 categorie restituisce un punteggio sia per "people_" che per "people_young" quando il modello è celebrities), i dettagli vengono associati alla corrispondenza di livello più generale ("people_" in questo esempio).

## <a name="error-responses"></a>Risposte agli errori

Questi errori sono identici a quelli di vision.analyze, con l'aggiunta dell'errore NotSupportedModel (HTTP 400), che può essere restituito in entrambi gli scenari per l'opzione 1 e 2. Per l'opzione 2 (analisi avanzata), se uno qualsiasi dei modelli specificati nei dettagli non viene riconosciuto, l'API restituisce un errore NotSupportedModel, anche se uno o più modelli risultano validi. Per scoprire quali modelli sono supportati, è possibile chiamare listModels.

## <a name="next-steps"></a>Passaggi successivi

Per usare l'API REST, vedere [Informazioni di riferimento per l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
