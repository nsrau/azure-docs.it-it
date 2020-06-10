---
title: Eseguire l'analisi del sentiment con l'API REST Analisi del testo
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come rilevare il sentiment nel testo con l'API REST Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 05/18/2020
ms.author: aahi
ms.openlocfilehash: acd8fae81baa7ad65b8d9c321c55a6311cbf4c72
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141246"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Procedura: Rilevare il sentiment con l'API REST Analisi del testo

La funzionalità Analisi del sentiment dell'API Analisi del testo valuta il testo e restituisce i punteggi e le etichette del sentiment per ogni frase. Questa funzionalità è utile per il rilevamento del sentiment positivo e negativo nei social media, nelle recensioni dei clienti, nei forum di discussione e così via. I modelli di intelligenza artificiale usati dall'API vengono forniti dal servizio, quindi è sufficiente inviare contenuto per l'analisi.

Dopo l'invio di una richiesta di analisi del sentiment, l'API restituisce le etichette del sentiment (ad esempio "negativo", "neutrale" e "positivo") e i punteggi di attendibilità a livello di frase e di documento.

Analisi del sentiment supporta una vasta gamma di linguaggi, mentre altri disponibili in anteprima. Per altre informazioni, vedere [Linguaggi supportati](../text-analytics-supported-languages.md).

## <a name="sentiment-analysis-versions-and-features"></a>Versioni e funzionalità di Analisi del sentiment

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funzionalità                                   | Analisi del sentiment v3 | Analisi del sentiment v3.1 (anteprima) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Metodi per richieste singole e batch    | X                     | X                                 |
| Punteggi ed etichettatura dei sentiment             | X                     | X                                 |
| [Contenitore Docker](text-analytics-how-to-install-containers.md) basato su Linux | X  |  |
| Opinion mining                            |                       | X                                 |

### <a name="sentiment-scoring-and-labeling"></a>Punteggi ed etichettatura dei sentiment

Analisi del sentiment v3 applica le etichette del sentiment al testo, che vengono restituite a livello di frase e documento, con un punteggio di attendibilità per ognuna. 

Le etichette sono `positive`, `negative` e `neutral`. A livello di documento, può anche essere restituita l'etichetta del sentiment `mixed`. Il sentiment del documento è determinato di seguito:

| Valutazione della frase                                                                            | Etichetta di documento restituita |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Nel documento è presente almeno una frase `positive`. Le restanti frasi sono `neutral`. | `positive`              |
| Nel documento è presente almeno una frase `negative`. Le restanti frasi sono `neutral`. | `negative`              |
| Nel documento è presente almeno una frase `negative` e almeno una frase `positive`.    | `mixed`                 |
| Tutte le frasi del documento sono `neutral`.                                                  | `neutral`               |

I punteggi di attendibilità sono compresi tra 1 e 0. I punteggi più prossimi a 1 indicano una maggiore attendibilità nella classificazione dell'etichetta, mentre i punteggi inferiori indicano un'attendibilità inferiore. La somma dei punteggi di attendibilità all'interno di ogni documento o frase è pari a 1.

### <a name="opinion-mining"></a>Opinion mining

Opinion mining è una funzionalità di Analisi del sentiment, a partire dalla versione 3.1-Preview.1. Nota anche come analisi del sentiment basata su aspetto nell'elaborazione del linguaggio naturale (NLP), questa funzionalità fornisce informazioni più granulari sulle opinioni relative ad aspetti del testo come gli attributi di prodotti o servizi.

Se ad esempio un cliente lascia il feedback su un hotel, come "la stanza è fantastica, ma il personale è scortese", la funzionalità di opinion mining individuerà gli aspetti nel testo e le opinioni e i sentiment associati:

| Aspetto | Opinione    | Valutazione |
|--------|------------|-----------|
| stanza   | fantastica      | positivo  |
| staff  | scortese | negativo  |

Per ottenere i risultati di opinion mining, è necessario includere il flag `opinionMining=true` in una richiesta di analisi del sentiment. I risultati di opinion mining verranno inclusi nella risposta dell'analisi del sentiment.

## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST 

### <a name="preparation"></a>Preparazione

L'analisi del sentiment genera risultati di qualità superiore quando viene eseguita su quantità ridotte di testo, a differenza dell'estrazione di espressioni chiave, che offre prestazioni migliori in blocchi di testo di maggiori dimensioni. Per ottenere risultati ottimali da entrambe le operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: ID, testo e lingua.

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta.

## <a name="structure-the-request"></a>Strutturare la richiesta

Creare una richiesta POST. È possibile [usare Postman](text-analytics-how-to-call-api.md) o la **console di test dell'API** nei collegamenti di riferimento seguenti per strutturarne e inviarne rapidamente una. 

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

[Informazioni di riferimento su Analisi del sentiment v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

#### <a name="version-31-preview1"></a>[Versione 3.1-preview.1](#tab/version-3-1)

[Informazioni di riferimento su Analisi del sentiment v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

---

### <a name="request-endpoints"></a>Endpoint di richiesta

Impostare l'endpoint HTTP per l'analisi del sentiment usando una risorsa di Analisi del testo in Azure oppure un'istanza di [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md). È necessario includere l'URL corretto per la versione che si vuole usare. Ad esempio:

> [!NOTE]
> È possibile trovare la chiave e l'endpoint per la risorsa Analisi del testo nel portale di Azure. Si trovano in **Gestione risorse** nella pagina **Avvio rapido** della risorsa. 

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

#### <a name="version-31-preview1"></a>[Versione 3.1-preview.1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

Per ottenere risultati di opinion mining, è necessario includere il parametro `opinionMining=true`. Ad esempio:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

Questo parametro è impostato su `false` per impostazione predefinita. 

---

Impostare un'intestazione della richiesta per includere la chiave dell'API Analisi del testo. Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

### <a name="example-sentiment-analysis-request"></a>Esempio di richiesta di Analisi del sentiment 

Di seguito è riportato un esempio di contenuto che può essere inviato per l'analisi del sentiment. Il formato della richiesta è identico per entrambe le versioni.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Per informazioni sulle dimensioni e sul numero delle richieste che è possibile inviare al minuto e al secondo, vedere la sezione [Limiti dei dati](../overview.md#data-limits) nella panoramica.

L'API Analisi del testo è senza stato. Non vengono archiviati dati nell'account e i risultati vengono restituiti immediatamente nella risposta.


### <a name="view-the-results"></a>View the results

L'analisi del sentiment restituisce un'etichetta del sentiment e un punteggio di attendibilità per l'intero documento e per ogni frase al suo interno. I punteggi più prossimi a 1 indicano una maggiore attendibilità nella classificazione dell'etichetta, mentre i punteggi inferiori indicano un'attendibilità inferiore. Un documento può includere più frasi e la somma dei punteggi di attendibilità all'interno di ogni documento o frase è pari a 1.

L'output viene restituito immediatamente. È possibile trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale. Importare quindi l'output in un'applicazione che consente di ordinare, cercare e modificare i dati. A causa del supporto multilingue e emoji, la risposta può contenere offset di testo. Per altre informazioni, vedere [come elaborare gli offset](../concepts/text-offsets.md).

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Risposta di esempio di Analisi del sentiment v3.0

Le risposte di Analisi del sentiment v3 contengono le etichette e i punteggi del sentiment per ogni frase e ogni documento analizzato.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-31-preview1"></a>[Versione 3.1-preview.1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Risposta di esempio di Analisi del sentiment v3.1

Analisi del sentiment v3.1 offre funzionalità di opinion mining oltre all'oggetto risposta nella scheda **Versione 3.0**. Nella risposta seguente la frase *The restaurant had great food and our waiter was friendly* include due aspetti, ovvero *food* e *waiter*. La proprietà `relations` di ogni aspetto contiene un valore `ref` con il riferimento URI agli oggetti `documents`, `sentences` e `opinions` associati.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
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

## <a name="summary"></a>Summary

In questo articolo sono stati appresi i concetti e il flusso di lavoro per l'analisi del sentiment eseguita con l'API Analisi del testo. In sintesi:

+ Analisi del sentiment è disponibile per determinate lingue.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST viene inviata a un endpoint `/sentiment` usando [una chiave di accesso e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizzati validi per la sottoscrizione.
+ L'output di risposta, costituito da un punteggio del sentiment per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, Ad esempio, Excel e Power BI.

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/text-analytics-sdk.md)
* [Novità](../whats-new.md)
