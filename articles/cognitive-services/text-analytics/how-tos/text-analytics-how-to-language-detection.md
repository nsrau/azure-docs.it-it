---
title: Come eseguire il rilevamento della lingua nell'API REST di Analisi del testo (Servizi cognitivi Microsoft in Azure) | Microsoft Docs
description: In questa esercitazione dettagliata si apprenderà come rilevare la lingua usando l'API REST di Analisi del testo in Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: f8e2d9a36533c298addcf42d3cb2061e9c2d1ac7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373868"
---
# <a name="how-to-detect-language-in-text-analytics"></a>Come rilevare la lingua in Analisi del testo

L'[API Rilevamento lingua](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) valuta il testo di input e per ogni documento restituisce gli identificatori della lingua con un punteggio indicante il livello di attendibilità dell'analisi. Analisi del testo riconosce fino a 120 lingue.

Questa funzionalità è utile per gli archivi di contenuto che includono testo arbitrario, in cui la lingua è sconosciuta. È possibile analizzare i risultati di questa analisi per determinare la lingua usata nel documento di input. La risposta restituisce inoltre un punteggio che riflette il livello di attendibilità del modello (un valore compreso tra 0 e 1).

## <a name="preparation"></a>Operazioni preliminari

È necessario disporre di documenti JSON nel formato seguente: id, testo

Le dimensioni dei singoli documenti devono essere inferiori a 5000 caratteri e si possono avere fino a 1000 elementi (ID) per ogni raccolta. La raccolta viene inviata nel corpo della richiesta. Di seguito è riportato un esempio di contenuto che può essere inviato per il rilevamento della lingua.

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ находится на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passaggio 1: Strutturare la richiesta

I dettagli sulla definizione della richiesta sono reperibili in [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Come richiamare l'API Analisi del testo). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta **POST**. Esaminare la documentazione dell'API per questa richiesta: [API Rilevamento lingua](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Impostare l'endpoint HTTP per il rilevamento della lingua. È necessario includere la risorsa `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Impostare un'intestazione della richiesta in modo da includere la chiave di accesso per le operazioni di Analisi del testo. Per altre informazioni, vedere [How to find endpoints and access keys](text-analytics-how-to-access-key.md) (Come trovare gli endpoint e le chiavi di accesso).

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Il servizio accetta fino a 100 richieste al minuto. Ogni richiesta può essere al massimo di 1 MB.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.


## <a name="step-3-view-results"></a>Passaggio 3: Visualizzare i risultati

Tutte le richieste POST restituiscono una risposta JSON formattata con gli ID e le proprietà rilevate.

L'output viene restituito immediatamente. Si possono trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati.

I risultati relativi alla richiesta di esempio dovrebbero avere un aspetto simile al codice JSON seguente. Si noti che si tratta di un singolo documento con più elementi. L'output è in inglese. Gli identificatori di lingua includono un nome descrittivo e un codice di lingua nel formato [ISO 639-1](https://www.iso.org/standard/22109.html).

Un punteggio positivo pari a 1.0 esprime il massimo livello possibile di attendibilità dell'analisi.



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>Contenuto ambiguo

Se l'analizzatore non riesce ad analizzare l'input, ad esempio nel caso in cui sia stato inviato un blocco di testo costituito esclusivamente da numeri arabi, viene restituito `(Unknown)`.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Contenuto in più lingue

Se all'interno dello stesso documento è presente contenuto in più lingue, viene visualizzata la lingua più rappresentata, ma con una classificazione positiva inferiore, che riflette l'attendibilità marginale della valutazione. Nell'esempio seguente l'input è costituito da una combinazione di inglese, spagnolo e francese. L'analizzatore conta i caratteri in ogni segmento per determinare la lingua prevalente.

**Input**

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Output**

L'output risultante è costituito dalla lingua prevalente, con un punteggio inferiore a 1.0, indicante un livello di attendibilità minore.

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>Riepilogo

In questo articolo si sono appresi i concetti e il flusso di lavoro per il rilevamento della lingua tramite Analisi del testo in Servizi cognitivi. Di seguito è riportata una rapida sintesi dei punti principali illustrati in precedenza:

+ L'[API Rilevamento lingua](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) è disponibile per 120 lingue.
+ I documenti JSON nel corpo della richiesta includono un ID e il testo.
+ La richiesta POST viene indirizzata a un endpoint `/languages` usando una [chiave di accesso personalizzata e un endpoint](text-analytics-how-to-access-key.md) valido per la sottoscrizione.
+ L'output di risposta, costituito da identificatori di lingua per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, tra cui Excel e Power BI, per citarne alcune.

## <a name="see-also"></a>Vedere anche  

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare la valutazione](text-analytics-how-to-sentiment-analysis.md)
