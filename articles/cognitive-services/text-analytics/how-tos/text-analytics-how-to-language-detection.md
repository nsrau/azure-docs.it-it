---
title: Rilevamento della lingua con l'API REST Analisi del testo | Microsoft Docs
description: Come rilevare la lingua usando l'API REST Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: 6f1e71b75aa68c8f4ea1fa8ed373da25dbb3c24b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304057"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>Esempio: Come rilevare la lingua con Analisi del testo

La funzionalità [Rilevamento lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) dell'API valuta il testo di input e per ogni documento restituisce gli identificatori della lingua con un punteggio indicante il livello di attendibilità dell'analisi.

Questa funzionalità è utile per gli archivi di contenuto che includono testo arbitrario, in cui la lingua è sconosciuta. È possibile analizzare i risultati di questa analisi per determinare la lingua usata nel documento di input. La risposta restituisce inoltre un punteggio che riflette il livello di attendibilità del modello (un valore compreso tra 0 e 1).

L'elenco esatto delle lingue per questa funzionalità non viene pubblicato, ma è in grado di rilevare un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali. 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

> [!TIP]
> Analisi del testo offre anche un'immagine del contenitore Docker basata su Linux per il rilevamento della lingua, di conseguenza è possibile [installare ed eseguire il contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) vicino ai dati.

## <a name="preparation"></a>Operazioni preliminari

È necessario disporre di documenti JSON nel formato seguente: ID, testo

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento e ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta. Di seguito è riportato un esempio di contenuto che può essere inviato per il rilevamento della lingua.

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
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passaggio 1: Strutturare la richiesta

I dettagli sulla definizione della richiesta sono reperibili in [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Come chiamare l'API Analisi del testo). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta **POST**. Esaminare la documentazione dell'API per la richiesta: [API Rilevamento lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)

+ Impostare l'endpoint HTTP per il rilevamento della lingua usando una risorsa di Analisi del testo in Azure oppure un [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) di cui è stata creata un'istanza. È necessario includere la risorsa `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`

+ Impostare un'intestazione della richiesta in modo da includere la chiave di accesso per le operazioni di Analisi del testo. Per altre informazioni, vedere [How to find endpoints and access keys](text-analytics-how-to-access-key.md) (Come trovare gli endpoint e le chiavi di accesso).

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Vedere la sezione relativa ai [limiti dei data](../overview.md#data-limits) nella panoramica per informazioni sulle dimensioni e il numero di richieste che è possibile inviare al minuto e al secondo.

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

## <a name="summary"></a>Summary

In questo articolo si sono appresi i concetti e il flusso di lavoro per il rilevamento della lingua tramite Analisi del testo in Servizi cognitivi. Di seguito è riportata una rapida sintesi dei punti principali illustrati in precedenza:

+ [Rilevamento lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) è disponibile per un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali.
+ I documenti JSON nel corpo della richiesta includono un ID e il testo.
+ La richiesta POST è a un endpoint `/languages`, usando [una chiave di accesso e un endpoint](text-analytics-how-to-access-key.md) personalizzati validi per la sottoscrizione.
+ L'output di risposta, costituito da identificatori di lingua per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, tra cui Excel e Power BI, per citarne alcune.

## <a name="see-also"></a>Vedere anche 

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare la valutazione](text-analytics-how-to-sentiment-analysis.md)
