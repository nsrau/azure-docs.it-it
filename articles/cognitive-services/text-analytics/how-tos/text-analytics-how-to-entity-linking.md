---
title: Come usare il collegamento delle entità nell'API REST Analisi del testo (Servizi cognitivi Microsoft in Azure) | Microsoft Docs
description: In questa esercitazione dettagliata si apprenderà come identificare e risolvere le entità usando l'API REST Analisi del testo nei Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374513"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Come identificare le entità collegate in Analisi del testo (anteprima)

L'[API di collegamento delle entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) prende del testo non strutturato e, per ogni documento JSON, restituisce un elenco di entità identificate senza ambiguità con collegamenti ad altre informazioni sul Web (Wikipedia e Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Collegamento delle entità rispetto al Riconoscimento di entità denominate

Nell'elaborazione del linguaggio naturale, è possibile confondere con facilità i concetti di collegamento delle entità e di riconoscimento di entità denominate (NER). Nella versione di anteprima dell'endpoint `entities` di Analisi del testo è supportato solo il collegamento delle entità.

Il collegamento delle entità è la capacità di identificare senza ambiguità l'identità di un'entità trovata nel testo (ad esempio, determinare se "Marte" viene usato per indicare il pianeta o il dio romano della guerra). Questo processo richiede la presenza di una knowledge base a cui sono collegate le entità riconosciute. Wikipedia è usata come knowledge base per l'endpoint `entities` di Analisi del testo.

### <a name="language-support"></a>Supporto per le lingue

L'uso del collegamento delle entità in varie lingue richiede l'uso di una corrispondente knowledge base in ciascuna lingua. Il collegamento delle entità in Analisi del testo significa che ogni lingua supportata dall'endpoint `entities` si collegherà al corpus Wikipedia corrispondente in tale lingua. Poiché le dimensioni del corpora variano tra le lingue, è prevista anche una variazione dell'entità collegata al richiamo della funzionalità.


## <a name="preparation"></a>Operazioni preliminari

È necessario disporre di documenti JSON nel formato seguente: id, testo, lingua

Per le lingue attualmente supportate, vedere [questo elenco](../text-analytics-supported-languages.md).

Le dimensioni dei documenti devono essere inferiori a 5.000 caratteri per documento e si possono avere fino a 1.000 elementi (ID) per ogni raccolta. La raccolta viene inviata nel corpo della richiesta. L'esempio seguente illustra il contenuto che è possibile inviare al collegamento delle entità.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Passaggio 1: Struttura della richiesta

I dettagli sulla definizione della richiesta sono reperibili in [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Come richiamare l'API Analisi del testo). Per comodità sono ridefiniti i punti seguenti:

+ Creare una richiesta **POST**. Esaminare la documentazione dell'API per questa richiesta: [API di collegamento delle entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Impostare l'endpoint HTTP per l'estrazione di frasi chiave. È necessario includere la risorsa `/entities`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Impostare un'intestazione della richiesta per includere la chiave di accesso per le operazioni di Analisi del testo. Per altre informazioni, vedere [How to find endpoints and access keys](text-analytics-how-to-access-key.md) (Come trovare gli endpoint e le chiavi di accesso).

+ Nel corpo della richiesta, fornire la raccolta di documenti JSON preparata per l'analisi

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Il servizio accetta fino a 100 richieste al minuto. Ogni richiesta può essere al massimo di 1 MB.

Tenere presente che il servizio è senza stato. Non ci sono dati archiviati nell'account. I risultati vengono restituiti immediatamente nella risposta.

## <a name="step-3-view-results"></a>Passaggio 3: Visualizzare i risultati

Tutte le richieste POST restituiscono una risposta JSON formattata con gli ID e le proprietà rilevate.

L'output viene restituito immediatamente. Si possono trasmettere i risultati in un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati.

Un esempio dell'output del collegamento delle entità è il seguente:

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

Quando è disponibile, la risposta include l'ID e l'URL di Wikipedia e l'ID Bing di ogni entità rilevata. Possono essere usati per migliorare ulteriormente l'applicazione con le informazioni relative all'entità collegata.


## <a name="summary"></a>Summary

In questo articolo si sono appresi i concetti e il flusso di lavoro per il collegamento delle entità usando Analisi del testo nei Servizi cognitivi. In breve:

+ L'[API di collegamento delle entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) è disponibile per le lingue selezionate.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST è a un endpoint `/entities`, usando [una chiave di accesso e un endpoint](text-analytics-how-to-access-key.md) personalizzati validi per la sottoscrizione.
+ L'output di risposta, che consiste di entità collegate (inclusi punteggi di attendibilità, offset e collegamenti Web per ogni ID di documento) può essere usato in qualsiasi applicazione

## <a name="see-also"></a>Vedere anche  

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Analisi del testo](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
