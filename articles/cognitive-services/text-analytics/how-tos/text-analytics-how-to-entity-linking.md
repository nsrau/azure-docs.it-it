---
title: Usare il riconoscimento di entità con l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Informazioni su come identificare e risolvere l'ambiguità dell'identità di un'entità trovata nel testo con l'API REST di Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/11/2020
ms.author: aahi
ms.openlocfilehash: cabde27591159b5751435a97a909a5f6f8c3081b
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518227"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Come usare il riconoscimento delle entità denominate in Analisi del testo

Il API Analisi del testo consente di usare un testo non strutturato e restituisce un elenco di entità ambiguità, con collegamenti ad altre informazioni sul Web. L'API supporta sia il riconoscimento delle entità denominate (NER) che il collegamento di entità.

### <a name="entity-linking"></a>Collegamento delle entità

Il collegamento di entità è la possibilità di identificare e risolvere l'ambiguità dell'identità di un'entità trovata nel testo (ad esempio, determinare se un'occorrenza della parola "Mars" si riferisce al pianeta o al Dio di guerra romano). Questo processo richiede la presenza di una Knowledge base in una lingua appropriata per collegare entità riconosciute nel testo. Il collegamento di entità USA [Wikipedia](https://www.wikipedia.org/) come questa Knowledge base.


### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

Il riconoscimento delle entità denominate (NER) è la possibilità di identificare entità diverse nel testo e di classificarle in classi o tipi predefiniti quali: persona, località, evento, prodotto e organizzazione.  

## <a name="named-entity-recognition-versions-and-features"></a>Versioni e funzionalità di riconoscimento entità denominate

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funzionalità                                                         | NER v 3.0 | NER v 3.1-Preview. 2 |
|-----------------------------------------------------------------|--------|----------|
| Metodi per richieste singole e batch                          | X      | X        |
| Riconoscimento delle entità espanso tra diverse categorie           | X      | X        |
| Separare gli endpoint per l'invio di entità di collegamento e richieste NER. | X      | X        |
| Riconoscimento di entità di `PII` informazioni personali () e di integrità ( `PHI` )        |        | x        |

Per informazioni, vedere Supporto per le [lingue](../language-support.md) .

## <a name="entity-types"></a>Tipi di entità

Il riconoscimento delle entità denominate V3 fornisce il rilevamento espanso tra più tipi. Attualmente, NER v 3.0 è in grado di riconoscere le entità nella [categoria di entità generale](../named-entity-types.md).

Il riconoscimento delle entità denominato v 3.1-Preview. 2 include le funzionalità di rilevamento della versione 3.0 e la possibilità di rilevare le informazioni personali ( `PII` ) usando l' `v3.1-preview.2/entities/recognition/pii` endpoint. È possibile utilizzare il `domain=phi` parametro facoltativo per rilevare le informazioni riservate sull'integrità ( `PHI` ). Per ulteriori informazioni, vedere l'articolo relativo alle [categorie di entità](../named-entity-types.md) e la sezione relativa agli [endpoint di richiesta](#request-endpoints) .


## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST

### <a name="preparation"></a>Preparazione

È necessario disporre di documenti JSON nel formato seguente: ID, testo, lingua.

Ogni documento deve essere composto da un massimo di 5.120 caratteri ed è possibile avere fino a 1.000 elementi (ID) per raccolta. La raccolta viene inviata nel corpo della richiesta.

### <a name="structure-the-request"></a>Strutturare la richiesta

Creare una richiesta POST. È possibile usare la **console di testing API** o di [posting](text-analytics-how-to-call-api.md) nei collegamenti seguenti per strutturare rapidamente e inviarne una. 

> [!NOTE]
> È possibile trovare la chiave e l'endpoint per la risorsa Analisi del testo nel portale di Azure. Si trovano in **Gestione risorse** nella pagina **Avvio rapido** della risorsa. 


### <a name="request-endpoints"></a>Endpoint di richiesta

#### <a name="version-31-preview2"></a>[Versione 3,1-Preview. 2](#tab/version-3-preview)

Il riconoscimento delle entità denominate `v3.1-preview.2` utilizza endpoint distinti per le richieste ner, pii ed Entity linking. Usare un formato URL seguente in base alla richiesta:

Collegamento di entità
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/linking`

[Informazioni di riferimento sul riconoscimento entità denominato versione 3,1-Preview per `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)

NER
* Entità generali- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/general`

[Informazioni di riferimento sul riconoscimento entità denominato versione 3,1-Preview per `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral)

Informazioni personali
* Informazioni personali ( `PII` )- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii`

È anche possibile usare il `domain=phi` parametro facoltativo per rilevare `PHI` le informazioni sull'integrità () nel testo. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi`

Si noti l'aggiunta della `redactedText` proprietà nella risposta JSON che contiene il testo di input modificato in cui le entità pii rilevate vengono sostituite da un * per ogni carattere delle entità.

[Informazioni di riferimento sul riconoscimento entità denominato versione 3,1-Preview per `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Il riconoscimento delle entità denominate V3 Usa endpoint distinti per le richieste NER e per il collegamento di entità. Usare un formato URL seguente in base alla richiesta:

Collegamento di entità
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Riferimento per il riconoscimento delle entità denominato versione 3,0 per `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Riferimento per il riconoscimento delle entità denominato versione 3,0 per `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Impostare un'intestazione della richiesta per includere la chiave dell'API Analisi del testo. Nel corpo della richiesta fornire i documenti JSON preparati.

### <a name="example-ner-request"></a>Richiesta NER di esempio 

Di seguito è riportato un esempio di contenuto che è possibile inviare all'API. Il formato della richiesta è identico per entrambe le versioni dell'API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Vedere la sezione relativa ai [limiti dei data](../overview.md#data-limits) nella panoramica per informazioni sulle dimensioni e il numero di richieste che è possibile inviare al minuto e al secondo.

L'API Analisi del testo è senza stato. Non vengono archiviati dati nell'account e i risultati vengono restituiti immediatamente nella risposta.

## <a name="view-results"></a>Visualizzazione dei risultati

Tutte le richieste POST restituiscono una risposta in formato JSON con gli ID e le proprietà dell'entità rilevata.

L'output viene restituito immediatamente. Si possono trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati. A causa del supporto multilingue e emoji, la risposta può contenere offset di testo. Per ulteriori informazioni [, vedere come elaborare gli offset di testo](../concepts/text-offsets.md) .

### <a name="example-responses"></a>Risposte di esempio

La versione 3 fornisce endpoint distinti per il collegamento generale NER, PII ed Entity. Di seguito sono riportate le risposte per entrambe le operazioni. 

#### <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-preview)

Esempio di risposta PII:
```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Esempio di risposta di collegamento di un'entità:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```


#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Esempio di risposta NER generale:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Riepilogo

In questo articolo si sono appresi i concetti e il flusso di lavoro per il collegamento delle entità usando Analisi del testo nei Servizi cognitivi. In sintesi:

* I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
* Le richieste POST vengono inviate a uno o più endpoint, usando una [chiave di accesso personalizzata e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) valido per la sottoscrizione.
* L'output di risposta, che consiste di entità collegate (inclusi punteggi di attendibilità, offset e collegamenti Web per ogni ID di documento) può essere usato in qualsiasi applicazione

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/text-analytics-sdk.md)
* [Novità](../whats-new.md)
