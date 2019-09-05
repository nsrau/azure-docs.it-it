---
title: "Esempio: Chiamare l'API di analisi immagine - Visione artificiale"
titleSuffix: Azure Cognitive Services
description: Informazioni su come chiamare l'API Visione artificiale con REST in Servizi cognitivi di Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 97b9e0defb3f349a6e202572bc0e3005d5d87e9c
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141195"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Esempio: Come chiamare l'API Visione artificiale

Questa guida illustra come chiamare l'API Visione artificiale tramite REST. Gli esempi sono scritti sia in C# usando la libreria client dell'API Visione artificiale, sia come chiamate POST HTTP/GET. Ci si concentrerà su:

- Come ottenere "Tags", "Description" e "Categories".
- Come ottenere informazioni "specifiche del dominio" (celebrities).

## <a name="prerequisites"></a>Prerequisiti

- URL o percorso di un'immagine archiviata in locale.
- Metodi di input supportati: file binario di immagine non elaborato di tipo application/octet-stream o URL di immagine
- Formati di immagine supportati: JPEG, PNG, GIF, BMP
- Dimensioni file di immagine: meno di 4 MB
- Dimensioni immagine: maggiori di 50 x 50 pixel
  
Negli esempi seguenti vengono illustrate le funzionalità seguenti:

1. Analisi di un'immagine e restituzione di una matrice di tag e una descrizione.
2. Analisi di un'immagine con un modello specifico di dominio (in particolare, il modello "celebrities") e recupero del risultato corrispondente in formato JSON.

Le funzionalità sono suddivise in:

- **Opzione uno:** analisi con ambito - analizzare solo un determinato modello
- **Opzione due:** analisi avanzata - analizzare per fornire dettagli aggiuntivi con le [86 categorie di tassonomia](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autorizzare la chiamata API

Per ogni chiamata all'API Visione artificiale è richiesta una chiave di sottoscrizione. Questa chiave deve essere passata tramite un parametro di stringa di query o essere specificata nell'intestazione della richiesta.

È possibile ottenere una chiave della versione di valutazione gratuita nella pagina [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Oppure seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere Visione artificiale e ottenere la chiave.

1. Passaggio della chiave di sottoscrizione tramite una stringa di query. Vedere di seguito un esempio di API Visione artificiale:

    ```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. Il passaggio della chiave di sottoscrizione può anche essere specificato nell'intestazione della richiesta HTTP:

    ```ocp-apim-subscription-key: <Your subscription key>```

1. Quando si usa la libreria client, la chiave di sottoscrizione viene passata tramite il costruttore di VisionServiceClient:

    ```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Caricare un'immagine nel servizio API Visione artificiale e ottenere tag, descrizioni e celebrità

Il modo semplice per eseguire la chiamata dell'API Visione artificiale è caricando direttamente un'immagine. Si invia a tal fine una richiesta "POST" con il tipo di contenuto application/octet-stream insieme ai dati letti dall'immagine. Per "Tags" e "Description" questo metodo di caricamento sarà lo stesso per tutte le chiamate all'API Visione artificiale. L'unica differenza saranno i parametri di query specificati dall'utente. 

Ecco come ottenere "Tags" e "Description" per una determinata immagine:

**Opzione uno:** ottenere l'elenco di "Tags" e una "Description"

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**Opzione due** ottenere solo un elenco di "Tags" o solo un elenco di "Description":

###### <a name="tags-only"></a>Solo tag:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Solo descrizione:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Ottenere l'analisi specifica del dominio (celebrità)

**Opzione uno:** analisi con ambito - analizzare solo un determinato modello
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Per questa opzione, tutti gli altri parametri di query {visualFeatures, details} non sono validi. Se si vogliono visualizzare tutti i modelli supportati, usare:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Opzione due:** analisi avanzata - analizzare per fornire dettagli aggiuntivi con le [86 categorie di tassonomia](../Category-Taxonomy.md)

Per le applicazioni in cui si vuole ottenere l'analisi delle immagini generica oltre a informazioni dettagliate da uno o più modelli specifici di dominio, l'API v1 viene estesa con il parametro di query models.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

Quando questo metodo viene richiamato, verrà chiamato per primo il classificatore delle 86 categorie. Se una delle categorie corrisponde a uno dei modelli noti/corrispondenti, verrà eseguito un secondo passaggio delle chiamate del classificatore. Ad esempio, se "details=all" o "details" include 'celebrities', verrà chiamato il modello celebrities dopo il classificatore delle 86 categorie e il risultato include la categoria person. Ciò aumenterà la latenza per gli utenti interessati alle celebrità, rispetto all'opzione uno.

Tutti i parametri di query v1 si comporteranno nello stesso modo in questo caso.  Se visualFeatures=categories non è specificato, verrà abilitato in modo implicito.

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
Tag  | `object` | Oggetto di primo livello per la matrice di tag
tags[].Name | `string`  | Parola chiave dal classificatore tags
tags[].Score    | `number`  | Punteggio di attendibilità compreso tra 0 e 1.
description  | `object` | Oggetto di primo livello per una descrizione.
description.tags[] |    `string`    | Elenco di tag.  Se l'attendibilità della capacità di produrre una didascalia non è sufficiente, i tag potrebbero essere l'unica informazione disponibile per il chiamante.
description.captions[].text | `string`  | Frase che descrive l'immagine.
description.captions[].confidence   | `number`  | Attendibilità per la frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recuperare e interpretare l'output JSON per i modelli specifici del dominio

**Opzione uno:** analisi con ambito - analizzare solo un determinato modello

L'output sarà una matrice di tag, ad esempio come in questo esempio:

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

**Opzione due:** analisi avanzata - analizzare per fornire dettagli aggiuntivi con le 86 categorie di tassonomia

Per i modelli specifici di dominio che usano l'opzione due (analisi avanzata), il tipo restituito delle categorie è esteso. Di seguito è riportato un esempio:

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

Il campo categories è un elenco di una o più delle [86 categorie](../Category-Taxonomy.md) della tassonomia originale. Si noti inoltre che le categorie che terminano con un carattere di sottolineatura corrisponderanno alla categoria in oggetto e ai relativi elementi figlio (ad esempio, people_ e people_group per il modello celebrities).

Campo   | Type  | Content
------|------|------|
Categorie | `object`   | Oggetto di primo livello
categories[].name    | `string` | Nome dalla tassonomia di 86 categorie
categories[].score  | `number`  | Punteggio di attendibilità compreso tra 0 e 1
categories[].detail  | `object?`      | Oggetto dettaglio facoltativo

Si noti che in caso di corrispondenza di più categorie (ad esempio, il classificatore delle 86 categorie restituisce un punteggio sia per people_ che per people_young quando model=celebrities), i dettagli vengono associati alla corrispondenza di livello più generale (people_ in questo esempio).

## <a name="errors-responses"></a>Risposte di errore

Sono identiche a vision.analyze, con l'aggiunta dell'errore NotSupportedModel (HTTP 400), che può essere restituito in entrambi gli scenari per l'opzione uno e due. Per l'opzione due (analisi avanzata), se uno qualsiasi dei modelli specificati nei dettagli non viene riconosciuto, l'API restituirà un errore NotSupportedModel, anche se uno o più modelli risultano validi.  Gli utenti possono chiamare listModels per scoprire quali modelli sono supportati.

## <a name="next-steps"></a>Passaggi successivi

Per usare l'API REST, vedere [Informazioni di riferimento per l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
