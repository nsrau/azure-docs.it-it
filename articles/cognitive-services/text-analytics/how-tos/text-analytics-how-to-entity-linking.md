---
title: Usare il riconoscimento di entità con l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Learn how to identify and disambiguate the identity of an entity found in text with the Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326633"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>How to use Named Entity Recognition in Text Analytics

The [Named Entity Recognition API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) takes unstructured text, and for each JSON document, returns a list of disambiguated entities with links to more information on the web (Wikipedia and Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Collegamento di entità e riconoscimento di entità denominate

The Text Analytics' `entities` endpoint supports both named entity recognition (NER) and entity linking.

### <a name="entity-linking"></a>Collegamento delle entità
Per collegamento delle entità si intende la capacità di identificare senza ambiguità l'identità di un'entità trovata nel testo, ad esempio per determinare se "Marte" viene usato per indicare il pianeta o il dio romano della guerra. Questo processo richiede la presenza di una knowledge base a cui sono collegate le entità riconosciute. Wikipedia è usata come knowledge base per l'endpoint `entities` di Analisi del testo.

### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)
Named entity recognition (NER) is the ability to identify different entities in text and categorize them into pre-defined classes, or types. 

## <a name="named-entity-recognition-v3-public-preview"></a>Named Entity Recognition v3 public preview

The next version of Named Entity Recognition is now available for public preview. It provides updates to both entity linking and Named Entity Recognition. Try it using the [API test console](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral).

:::row:::
    :::column span="":::
        **Funzionalità**
    :::column-end:::
    ::: column span="":::
        **Descrizione** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Expanded entity types and subtypes
    :::column-end:::
    :::column span="":::
     Expanded classification and detection for several named entity types.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Separate request endpoints 
    :::column-end:::
    :::column span="":::
        Separate endpoints for sending entity linking and NER requests.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parameter
    :::column-end:::
    :::column span="":::
        An optional parameter for choosing a version of the Text Analytics model. Currently only the default model is available for use.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Tipi di entità

Named Entity Recognition v3 provides expanded detection across multiple types. Currently, NER v3 can recognize the following categories of entities. For a detailed list of supported entities and languages, see the [Named entity types](../named-entity-types.md) article.

* Informazioni di carattere generale
* Personal Information 

### <a name="request-endpoints"></a>Request endpoints

Named Entity Recognition v3 uses separate endpoints for NER and entity linking requests. Use a URL format below based on your request:

NER
* General entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Personal information entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entity linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Gestione della versione dei modelli

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Supported Types for Named Entity Recognition v2

> [!NOTE]
> The following entities are supported by Named Entity Recognition(NER) version 2. [NER v3](#named-entity-recognition-v3-public-preview) is in public preview, and greatly expands the number and depth of the entities recognized in text.   

| Type  | SubType | Esempio |
|:-----------   |:------------- |:---------|
| Persona        | N/D\*         | "Jeff", "Bill Gates"     |
| Località      | N/D\*         | "Redmond, Washington", "Paris"  |
| Organizzazione  | N/D\*         | "Microsoft"   |
| Quantità      | Numero        | "6", "sei"     |
| Quantità      | Percentuale    | "50%", "cinquanta percento"|
| Quantità      | Ordinale       | "2°", "secondo"     |
| Quantità      | Età           | "90 giorni", "30 anni"    |
| Quantità      | Valuta      | "$ 10,99"     |
| Quantità      | Dimensione     | "10 miglia", "40 cm"     |
| Quantità      | Temperatura   | "32 gradi"    |
| Data e ora      | N/D\*         | "4 febbraio 2012 18:30"      |
| Data e ora      | Data          | "2 maggio 2017", "02/05/2017"   |
| Data e ora      | Durata          | "8", "8:00"  |
| Data e ora      | Intervallo di date     | "dal 2 maggio al 5 maggio"    |
| Data e ora      | Intervallo orario     | "dalle 18 alle 19"     |
| Data e ora      | Duration      | "1 minuto e 45 secondi"   |
| Data e ora      | Configurazione           | "ogni martedì"     |
| URL           | N/D\*         | "https:\//www.bing.com"    |
| Indirizzo di posta elettronica         | N/D\*         | "support@contoso.com" |
| US Phone Number  | N/D\*         | (US phone numbers only) "(312) 555-0176" |
| Indirizzo IP    | N/D\*         | "10.0.0.100" |

\*A seconda delle entità immesse ed estratte, alcune entità possono omettere `SubType`.  All the supported entity types listed are available only for the English, Chinese-Simplified, French, German, and Spanish languages.

### <a name="language-support"></a>Supporto delle lingue

L'uso del collegamento delle entità in varie lingue richiede l'uso di una corrispondente knowledge base in ciascuna lingua. Il collegamento delle entità in Analisi del testo significa che ogni lingua supportata dall'endpoint `entities` si collegherà al corpus Wikipedia corrispondente in tale lingua. Poiché le dimensioni del corpora variano tra le lingue, è prevista anche una variazione dell'entità collegata al richiamo della funzionalità. See the [language support](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) article for more information.

## <a name="preparation"></a>Operazioni preliminari

You must have JSON documents in this format: ID, text, language

Per le lingue attualmente supportate, vedere [questo elenco](../text-analytics-supported-languages.md).

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento e ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta. L'esempio seguente illustra il contenuto che è possibile inviare al collegamento delle entità.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passaggio 1: Strutturare la richiesta

I dettagli sulla definizione della richiesta sono reperibili in [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Come chiamare l'API Analisi del testo). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta **POST**. Review the API documentation for this request: [Entities API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Impostare l'endpoint HTTP per l'estrazione delle frasi chiave usando una risorsa di Analisi del testo in Azure oppure un'istanza di un [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md). You must include `/text/analytics/v2.1/entities`. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Set a request header to include [the access key](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) for Text Analytics operations.

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Vedere la sezione relativa ai [limiti dei data](../overview.md#data-limits) nella panoramica per informazioni sulle dimensioni e il numero di richieste che è possibile inviare al minuto e al secondo.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.

## <a name="step-3-view-results"></a>Passaggio 3: Visualizzare i risultati

Tutte le richieste POST restituiscono una risposta JSON formattata con gli ID e le proprietà rilevate.

L'output viene restituito immediatamente. Si possono trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati.

Un esempio dell'output del collegamento delle entità è il seguente:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Summary

In questo articolo si sono appresi i concetti e il flusso di lavoro per il collegamento delle entità usando Analisi del testo nei Servizi cognitivi. In sintesi:

+ L'[API entità](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) è disponibile per le lingue selezionate.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST viene indirizzata a un endpoint `/entities` usando una [chiave di accesso personalizzata e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) valido per la sottoscrizione.
+ L'output di risposta, che consiste di entità collegate (inclusi punteggi di attendibilità, offset e collegamenti Web per ogni ID di documento) può essere usato in qualsiasi applicazione

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Analisi del testo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Panoramica di Analisi del testo](../overview.md)
* [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)</br>
* [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712)
