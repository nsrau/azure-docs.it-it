---
title: Usare il riconoscimento di entità con l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Informazioni su come riconoscere le entità usando l'API REST Analisi del testo.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: e81428d5bdffb65b5e61a7aba7496da275f249a5
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230557"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Come usare il riconoscimento delle entità denominate in Analisi del testo (anteprima)

L'[API di riconoscimento entità denominate](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) prende testo non strutturato e, per ogni documento JSON, restituisce un elenco di entità identificate senza ambiguità con collegamenti ad altre informazioni sul Web (Wikipedia e Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Collegamento di entità e riconoscimento di entità denominate

L'endpoint `entities` di Analisi del testo supporta sia il riconoscimento delle entità denominate (NER) sia il collegamento di entità.

### <a name="entity-linking"></a>Collegamento delle entità
Per collegamento delle entità si intende la capacità di identificare senza ambiguità l'identità di un'entità trovata nel testo, ad esempio per determinare se "Marte" viene usato per indicare il pianeta o il dio romano della guerra. Questo processo richiede la presenza di una knowledge base a cui sono collegate le entità riconosciute. Wikipedia è usata come knowledge base per l'endpoint `entities` di Analisi del testo.

In Analisi del testo [versione 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) è disponibile solo il collegamento delle entità.

### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)
Il riconoscimento delle entità denominate (NER) è la possibilità di identificare le diverse entità in formato testo e classificarle in classi già definite. Le classi di entità supportate sono elencate di seguito.

In Analisi del testo [versione 2.1-preview](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) sono disponibili sia il collegamento delle entità che il riconoscimento delle entità denominate (NER).

### <a name="language-support"></a>Supporto per le lingue

L'uso del collegamento delle entità in varie lingue richiede l'uso di una corrispondente knowledge base in ciascuna lingua. Il collegamento delle entità in Analisi del testo significa che ogni lingua supportata dall'endpoint `entities` si collegherà al corpus Wikipedia corrispondente in tale lingua. Poiché le dimensioni del corpora variano tra le lingue, è prevista anche una variazione dell'entità collegata al richiamo della funzionalità.

## <a name="supported-types-for-named-entity-recognition"></a>Tipi supportati per il riconoscimento delle entità denominate

| type  | SubType | Esempio |
|:-----------   |:------------- |:---------|
| Person        | N/D\*         | "Jeff", "Bill Gates"     |
| Località      | N/D\*         | "Redmond, Washington", "Parigi"  |
| Organizzazione  | N/D\*         | "Microsoft"   |
| Quantità      | Number        | "6", "sei"     | 
| Quantità      | Percentuale    | "50%", "cinquanta percento"| 
| Quantità      | Ordinale       | "2°", "secondo"     | 
| Quantità      | NumberRange   | "da 4 a 8"     | 
| Quantità      | Age           | "90 giorni", "30 anni"    | 
| Quantità      | Valuta      | "$ 10,99"     | 
| Quantità      | Dimensione     | "10 miglia", "40 cm"     | 
| Quantità      | Temperatura   | "32 gradi"    |
| Datetime      | N/D\*         | "4 febbraio 2012 18:30"      | 
| Datetime      | Data          | "2 maggio 2017", "02/05/2017"   | 
| Data/Ora     | Tempo          | "8", "8:00"  | 
| Datetime      | DateRange     | "Dal 2 maggio al 5 maggio"    | 
| Datetime      | TimeRange     | "dalle 18 alle 19"     | 
| Datetime      | Duration      | "1 minuto e 45 secondi"   | 
| Datetime      | Configurazione           | "ogni martedì"     | 
| Datetime      | TimeZone      |    | 
| URL           | N/D\*         | "http://www.bing.com"    |
| Email         | N/D\*         | "support@contoso.com" |
\*A seconda delle entità immesse ed estratte, alcune entità possono omettere `SubType`.



## <a name="preparation"></a>Operazioni preliminari

È necessario disporre di documenti JSON nel formato seguente: id, testo, lingua

Per le lingue attualmente supportate, vedere [questo elenco](../text-analytics-supported-languages.md).

Le dimensioni dei documenti devono essere inferiori a 5.000 caratteri per documento e si possono avere fino a 1.000 elementi (ID) per ogni raccolta. La raccolta viene inviata nel corpo della richiesta. L'esempio seguente illustra il contenuto che è possibile inviare al collegamento delle entità.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Passaggio 1: Struttura della richiesta

I dettagli sulla definizione della richiesta sono reperibili in [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Come chiamare l'API Analisi del testo). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta **POST**. Esaminare la documentazione dell'API per questa richiesta: [API di collegamento delle entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Impostare l'endpoint HTTP per l'estrazione di entità. È necessario includere la risorsa `/entities`: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

+ Impostare un'intestazione della richiesta in modo da includere la chiave di accesso per le operazioni di Analisi del testo. Per altre informazioni, vedere [How to find endpoints and access keys](text-analytics-how-to-access-key.md) (Come trovare gli endpoint e le chiavi di accesso).

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Il servizio accetta fino a 100 richieste al minuto. Ogni richiesta può essere al massimo di 1 MB.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.

## <a name="step-3-view-results"></a>Passaggio 3: Visualizzare i risultati

Tutte le richieste POST restituiscono una risposta JSON formattata con gli ID e le proprietà rilevate.

L'output viene restituito immediatamente. Si possono trasmettere i risultati in un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati.

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

+ L'[API entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) è disponibile per le lingue selezionate.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST è a un endpoint `/entities`, usando [una chiave di accesso e un endpoint](text-analytics-how-to-access-key.md) personalizzati validi per la sottoscrizione.
+ L'output di risposta, che consiste di entità collegate (inclusi punteggi di attendibilità, offset e collegamenti Web per ogni ID di documento) può essere usato in qualsiasi applicazione

## <a name="see-also"></a>Vedere anche  

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Analisi del testo](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
