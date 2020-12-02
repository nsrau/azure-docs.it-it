---
title: Chiamare l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come chiamare i servizi cognitivi di Azure Analisi del testo l'API REST e il post.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 90a4da2aadbbdf07d851e4407d2d417fc76d32af
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512325"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Come chiamare l'API REST Analisi del testo

In questo articolo viene usata l'API REST [analisi del testo e l'](https://www.postman.com/downloads/) utente per illustrare i concetti chiave. L'API fornisce diversi endpoint sincroni e asincroni per l'utilizzo delle funzionalità del servizio. 

## <a name="using-the-api-asynchronously"></a>Uso dell'API in modo asincrono

A partire dalla versione 3.1-Preview. 3, il API Analisi del testo fornisce due endpoint asincroni: 

* L' `/analyze` endpoint per analisi del testo consente di analizzare lo stesso set di documenti di testo con più funzionalità di analisi del testo in una chiamata API. In precedenza, per usare più funzionalità, era necessario effettuare chiamate API separate per ogni operazione. Considerare questa funzionalità quando è necessario analizzare set di documenti di grandi dimensioni con più di una funzionalità Analisi del testo.

* `/health`Endpoint per analisi del testo per l'integrità, che può estrarre ed etichettare le informazioni mediche rilevanti da documenti clinici.  

Vedere la tabella seguente per vedere quali funzionalità possono essere usate in modo asincrono. Si noti che solo alcune funzionalità possono essere chiamate dall' `/analyze` endpoint. 

| Funzionalità | Sincrono | Asincrono |
|--|--|--|
| Rilevamento della lingua | ✔ |  |
| Analisi del sentiment | ✔ |  |
| Opinion mining | ✔ |  |
| Estrazione di frasi chiave | ✔ | ✔* |
| Riconoscimento di entità denominate (incluse le informazioni personali e PHI) | ✔ | ✔* |
| Analisi del testo per l'integrità (contenitore) | ✔ |  |
| Analisi del testo per l'integrità (API) |  | ✔  |

`*` -Chiamato in modo asincrono tramite l' `/analyze` endpoint.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Prerequisiti


> [!NOTE]
> Se si desidera utilizzare gli endpoint o, sarà necessario disporre di una risorsa Analisi del testo utilizzando un piano [tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) standard `/analyze` `/health` .

1.  Per prima cosa, passare alla [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e creare una nuova risorsa di analisi del testo, se non ne è già presente uno. Scegliere il piano **tariffario standard (S)** se si desidera utilizzare gli `/analyze` `/health` endpoint o.

2.  Selezionare l'area che si vuole usare per l'endpoint.  Si noti `/analyze` che gli `/health` endpoint e sono disponibili solo nelle aree seguenti: Stati Uniti occidentali 2, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale ed Europa occidentale.

3.  Creare la risorsa Analisi del testo e passare al pannello "chiavi ed endpoint" nella parte sinistra della pagina. Copiare la chiave da usare in un secondo momento quando si chiamano le API. Questa operazione verrà aggiunta in un secondo momento come valore per l' `Ocp-Apim-Subscription-Key` intestazione.


<a name="json-schema"></a>

## <a name="api-request-format"></a>Formato richiesta API

#### <a name="synchronous"></a>[Sincrono](#tab/synchronous)

Il formato delle richieste API è lo stesso per tutte le operazioni sincrone. I documenti vengono inviati in un oggetto JSON come testo non strutturato non elaborato. XML non è supportato. Lo schema JSON è costituito dagli elementi descritti di seguito.

| Elemento | Valori validi | Necessaria? | Utilizzo |
|---------|--------------|-----------|-------|
|`id` |Il tipo di dati è stringa, ma in pratica gli ID documento tendono a essere numeri interi. | Necessario | Il sistema usa gli ID immessi per strutturare l'output. Per ogni ID della richiesta vengono generati codici di lingua, frasi chiave e punteggi di sentiment.|
|`text` | Testo non elaborato non strutturato, composto da un massimo di 5.120 caratteri. | Necessario | Per il rilevamento della lingua, il testo può essere espresso in qualsiasi lingua. Per l'analisi del sentiment, l'estrazione delle frasi chiave e l'identificazione delle entità, il testo deve essere in una [lingua supportata](../language-support.md). |
|`language` | Codice [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) di 2 caratteri per una [lingua supportata](../language-support.md) | Varia | Richiesto per l'analisi del sentiment, l'estrazione delle frasi chiave e il collegamento delle entità; facoltativo per il rilevamento della lingua. Se lo si omette non si verifica nessun errore ma l'analisi risulta più debole. Il codice della lingua deve corrispondere al `text` fornito. |

Di seguito è riportato un esempio di una richiesta API per gli endpoint sincroni del Analisi del testo. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[Analizzare](#tab/analyze)

> [!NOTE]
> L'ultima versione preliminare della libreria client di Analisi del testo consente di chiamare operazioni di analisi asincrone utilizzando un oggetto client. È possibile trovare esempi su GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

L' `/analyze` endpoint consente di scegliere quale delle funzionalità di analisi del testo supportate si vuole usare in una singola chiamata API. Questo endpoint attualmente supporta:

* estrazione di frasi chiave 
* Riconoscimento di entità denominate (incluse le informazioni personali e PHI)

| Elemento | Valori validi | Necessaria? | Utilizzo |
|---------|--------------|-----------|-------|
|`displayName` | Stringa | Facoltativo | Utilizzato come nome visualizzato per l'identificatore univoco del processo.|
|`analysisInput` | Include il `documents` campo seguente | Necessario | Contiene le informazioni per i documenti che si desidera inviare. |
|`documents` | Include i `id` `text` campi e seguenti | Necessario | Contiene informazioni per ogni documento inviato e il testo non elaborato del documento. |
|`id` | Stringa | Necessario | Gli ID forniti vengono usati per strutturare l'output. |
|`text` | Testo non elaborato non strutturato, composto da un massimo di 125.000 caratteri. | Necessario | Deve essere nella lingua inglese, che è l'unica lingua attualmente supportata. |
|`tasks` | Include le funzionalità di Analisi del testo seguenti `entityRecognitionTasks` : `keyPhraseExtractionTasks` o `entityRecognitionPiiTasks` . | Necessario | Una o più delle funzionalità di Analisi del testo che si desidera utilizzare. Si noti che `entityRecognitionPiiTasks` dispone `domain` di un parametro facoltativo che può essere impostato su `pii` o `phi` . Se non è specificato, l'impostazione predefinita del sistema è `pii` . |
|`parameters` | Include i `model-version` `stringIndexType` campi e seguenti | Necessario | Questo campo è incluso nelle attività della funzionalità indicate in precedenza. Contengono informazioni sulla versione del modello che si desidera utilizzare e il tipo di indice. |
|`model-version` | Stringa | Necessario | Consente di specificare la versione del modello chiamata che si desidera utilizzare.  |
|`stringIndexType` | Stringa | Necessario | Specificare il decodificatore di testo corrispondente all'ambiente di programmazione.  I tipi supportati sono `textElement_v8` (impostazione predefinita), `unicodeCodePoint` , `utf16CodeUnit` . Per ulteriori informazioni, vedere l' [articolo offset del testo](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
|`domain` | Stringa | Facoltativo | Si applica solo come parametro all' `entityRecognitionPiiTasks` attività e può essere impostato su `pii` o `phi` . Il valore predefinito è `pii` se non è specificato.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Analisi del testo per la sanità](#tab/health)

Il formato delle richieste API per la Analisi del testo per l'API ospitata sull'integrità è identico a quello del relativo contenitore. I documenti vengono inviati in un oggetto JSON come testo non strutturato non elaborato. XML non è supportato. Lo schema JSON è costituito dagli elementi descritti di seguito.  Compilare e inviare il modulo di [richiesta di servizi cognitivi](https://aka.ms/csgate) per richiedere l'accesso alla analisi del testo per l'anteprima pubblica dell'integrità. Non verranno addebitate Analisi del testo per l'utilizzo dell'integrità. 

| Elemento | Valori validi | Necessaria? | Utilizzo |
|---------|--------------|-----------|-------|
|`id` |Il tipo di dati è stringa, ma in pratica gli ID documento tendono a essere numeri interi. | Necessario | Il sistema usa gli ID immessi per strutturare l'output. |
|`text` | Testo non elaborato non strutturato, composto da un massimo di 5.120 caratteri. | Necessario | Si noti che attualmente è supportato solo il testo in lingua inglese. |
|`language` | Codice [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) di 2 caratteri per una [lingua supportata](../language-support.md) | Necessario | `en`Attualmente è supportato solo. |

Di seguito è riportato un esempio di una richiesta API per la Analisi del testo per gli endpoint di integrità. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Per informazioni sulle tariffe e sui limiti di dimensioni per l'invio di dati al API Analisi del testo, vedere l'articolo relativo ai [limiti di velocità e dati](../concepts/data-limits.md) .


## <a name="set-up-a-request"></a>Configurare una richiesta 

In postazione (o un altro strumento di test dell'API Web) aggiungere l'endpoint per la funzionalità che si vuole usare. Usare la tabella seguente per trovare il formato dell'endpoint appropriato e sostituire `<your-text-analytics-resource>` con l'endpoint della risorsa. Ad esempio:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Sincrono](#tab/synchronous)

| Funzionalità | Tipo di richiesta | Endpoint delle risorse |
|--|--|--|
| Rilevamento della lingua | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Analisi del sentiment | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Opinion mining | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Estrazione di frasi chiave | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Riconoscimento entità denominata-generale | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Riconoscimento entità denominata-informazioni personali | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Riconoscimento entità denominata-PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[Analizzare](#tab/analyze)

| Funzionalità | Tipo di richiesta | Endpoint delle risorse |
|--|--|--|
| Invia processo di analisi | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Ottenere lo stato e i risultati dell'analisi | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Analisi del testo per la sanità](#tab/health)

| Funzionalità | Tipo di richiesta | Endpoint delle risorse |
|--|--|--|
| Inviare Analisi del testo per il processo di integrità  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Ottenere lo stato e i risultati del processo | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Annulla processo | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

Dopo aver eseguito l'endpoint, nel post (o in un altro strumento di test dell'API Web):

1. Scegliere il tipo di richiesta per la funzionalità che si desidera utilizzare.
2. Incollare l'endpoint dell'operazione corretta desiderata nella tabella precedente.
3. Impostare le tre intestazioni della richiesta:

   + `Ocp-Apim-Subscription-Key`: la chiave di accesso, ottenuta da portale di Azure
   + `Content-Type`: application/json
   + `Accept`: application/json

    Se si usa il post, la richiesta dovrebbe avere un aspetto simile allo screenshot seguente, presupponendo che si tratti di un `/keyPhrases` endpoint.
    
    ![Screenshot di richiesta con l'endpoint e intestazioni](../media/postman-request-keyphrase-1.png)
    
4. Scegliere **RAW** per il formato del **corpo**
    
    ![Screenshot di richiesta con impostazioni relative al corpo](../media/postman-request-body-raw.png)

5. Incollare alcuni documenti JSON in un formato valido. Usare gli esempi nella sezione relativa al **formato della richiesta dell'API** riportata sopra. per ulteriori informazioni ed esempi, vedere gli argomenti seguenti:

      + [Rilevamento della lingua](text-analytics-how-to-language-detection.md)
      + [Estrazione delle frasi chiave](text-analytics-how-to-keyword-extraction.md)
      + [Analisi del sentiment](text-analytics-how-to-sentiment-analysis.md)
      + [Riconoscimento delle entità](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Inviare la richiesta

Inviare la richiesta dell'API. Se è stata effettuata la chiamata a un endpoint sincrono, la risposta verrà visualizzata immediatamente, come singolo documento JSON, con un elemento per ogni ID documento specificato nella richiesta.

Se è stata effettuata la chiamata agli `/analyze` endpoint asincroni o `/health` , controllare che sia stato ricevuto un codice di risposta 202. è necessario ottenere la risposta per visualizzare i risultati:

1. Nella risposta dell'API trovare l'oggetto `Operation-Location` dall'intestazione, che identifica il processo inviato all'API. 
2. Creare una richiesta GET per l'endpoint usato. vedere la [tabella precedente](#set-up-a-request) per il formato dell'endpoint ed esaminare la [documentazione di riferimento dell'API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Ad esempio:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Aggiungere la `Operation-Location` alla richiesta.

4. La risposta sarà un singolo documento JSON, con un elemento per ogni ID documento specificato nella richiesta.

## <a name="example-api-responses"></a>Risposte API di esempio
 
# <a name="synchronous"></a>[Sincrono](#tab/synchronous)

Le risposte degli endpoint sincroni variano a seconda dell'endpoint utilizzato. Per le risposte di esempio, vedere gli articoli seguenti.

+ [Rilevamento della lingua](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Estrazione delle frasi chiave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analisi del sentiment](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Riconoscimento delle entità](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[Analizzare](#tab/analyze)

In caso di esito positivo, la richiesta GET all' `/analyze` endpoint restituirà un oggetto contenente le attività assegnate. Ad esempio `keyPhraseExtractionTasks`. Queste attività contengono l'oggetto Response della funzionalità Analisi del testo appropriata. Vedi gli articoli seguenti per altre informazioni.

+ [Estrazione delle frasi chiave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Riconoscimento delle entità](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[Analisi del testo per la sanità](#tab/health)

Vedere l'articolo seguente per altre informazioni sull'Analisi del testo per la risposta API asincrona di integrità:

+ [Analisi del testo per la sanità](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Domande frequenti](../text-analytics-resource-faq.md)</br>
* [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712)
* [Uso della libreria client di Analisi del testo](../quickstarts/text-analytics-sdk.md)
* [Novità](../whats-new.md)
