---
title: Rilevare la lingua con l'API REST Analisi del testo | Microsoft Docs
description: Rilevare la lingua con l'API REST Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e1adeb34cf999f471bb183e4d7de9c65427252bb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986518"
---
# <a name="example-detect-language-with-text-analytics"></a>Esempio: Rilevare la lingua con Analisi del testo

La funzionalità [Rilevamento lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) dell'API REST Analisi del testo di Azure valuta l'input di testo per ogni documento e restituisce identificatori di lingua con un punteggio indicante il livello di attendibilità dell'analisi.

Questa funzionalità è utile per gli archivi di contenuto che includono testo arbitrario, in cui la lingua è sconosciuta. È possibile analizzare i risultati di questa analisi per determinare la lingua usata nel documento di input. La risposta restituisce anche un punteggio che riflette l'attendibilità del modello. Il valore del punteggio è compreso tra 0 e 1.

La funzionalità Rilevamento lingua può rilevare un'ampia gamma di lingue, varianti e dialetti e alcune lingue regionali o culturali. L'elenco esatto delle lingue per questa funzionalità non è stato pubblicato.

In caso di contenuto espresso in un lingua di uso meno frequente, si può provare la funzionalità Rilevamento lingua per verificare se viene restituito un codice. La risposta per le lingue non rilevabili è `unknown`.

> [!TIP]
> Analisi del testo offre anche un'immagine del contenitore Docker basata su Linux per il rilevamento della lingua, di conseguenza è possibile [installare ed eseguire il contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) vicino ai dati.

## <a name="preparation"></a>Operazioni preliminari

È necessario disporre di documenti JSON nel formato seguente: ID e testo.

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta. Di seguito è riportato un esempio del contenuto che può essere inviato per il rilevamento della lingua:

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

Per altre informazioni sulla definizione della richiesta, vedere [Chiamare l'API Analisi del testo](text-analytics-how-to-call-api.md). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta POST. Per esaminare la documentazione dell'API per la richiesta, vedere [API Rilevamento lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Impostare l'endpoint HTTP per il rilevamento della lingua. Usare una risorsa di Analisi del testo in Azure oppure un [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) di cui è stata creata un'istanza. È necessario includere la risorsa `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Impostare un'intestazione della richiesta in modo da includere la chiave di accesso per le operazioni di Analisi del testo. Per altre informazioni, vedere [Trovare gli endpoint e le chiavi di accesso](text-analytics-how-to-access-key.md).

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Inviare la richiesta POST

Al momento della ricezione della richiesta viene eseguita l'analisi. Per informazioni sulle dimensioni e sul numero delle richieste che è possibile inviare al minuto e al secondo, vedere la sezione [Limiti dei dati](../overview.md#data-limits) nella panoramica.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.


## <a name="step-3-view-the-results"></a>Passaggio 3: View the results

Tutte le richieste POST restituiscono una risposta in formato JSON con gli ID e le proprietà rilevate.

L'output viene restituito immediatamente. È possibile trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale. Importare quindi l'output in un'applicazione che consente di ordinare, cercare e modificare i dati.

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

Se l'analizzatore non riesce ad analizzare l'input, restituisce `(Unknown)`. Ciò si verifica ad esempio se si invia un blocco di testo costituito esclusivamente da numeri arabi.

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

Se all'interno dello stesso documento è presente contenuto in più lingue, viene restituita la lingua più rappresentata, ma con una classificazione positiva inferiore, che riflette l'attendibilità marginale della valutazione. Nell'esempio seguente l'input è costituito da una combinazione di inglese, spagnolo e francese. L'analizzatore conta i caratteri in ogni segmento per determinare la lingua prevalente.

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

L'output risultante è costituito dalla lingua prevalente, con un punteggio inferiore a 1 che indica un livello di attendibilità minore.

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

In questo articolo si sono appresi i concetti e il flusso di lavoro per il rilevamento della lingua con Analisi del testo di Servizi cognitivi di Azure. È stato illustrato e dimostrato quanto segue:

+ [Rilevamento lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) è disponibile per un'ampia gamma di lingue, varianti e dialetti e alcune lingue regionali o culturali.
+ I documenti JSON nel corpo della richiesta includono un ID e il testo.
+ La richiesta POST viene inviata a un endpoint `/languages` usando [una chiave di accesso e un endpoint](text-analytics-how-to-access-key.md) personalizzati validi per la sottoscrizione.
+ L'output di risposta è costituito da identificatori di lingua per ogni ID documento. L'output può essere trasmesso a qualsiasi app che accetta JSON, ad esempio Excel e Power BI, per citarne alcune.

## <a name="see-also"></a>Vedere anche 

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare la valutazione](text-analytics-how-to-sentiment-analysis.md)
