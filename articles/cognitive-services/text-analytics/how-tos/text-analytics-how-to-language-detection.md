---
title: Rilevare la lingua con l'API REST Analisi del testo
titleSuffix: Azure Cognitive Services
description: Rilevare la lingua con l'API REST Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 10/16/2020
ms.author: aahi
ms.openlocfilehash: 7214915e28158fe5dbb7b350e175b068afed1244
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166200"
---
# <a name="example-detect-language-with-text-analytics"></a>Esempio: Rilevare la lingua con Analisi del testo

La funzionalità [Rilevamento lingua](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) dell'API REST Analisi del testo di Azure valuta l'input di testo per ogni documento e restituisce identificatori di lingua con un punteggio indicante il livello di attendibilità dell'analisi.

Questa funzionalità è utile per gli archivi di contenuto che includono testo arbitrario, in cui la lingua è sconosciuta. È possibile analizzare i risultati di questa analisi per determinare la lingua usata nel documento di input. La risposta restituisce anche un punteggio che riflette l'attendibilità del modello. Il valore del punteggio è compreso tra 0 e 1.

La funzionalità Rilevamento lingua può rilevare un'ampia gamma di lingue, varianti e dialetti e alcune lingue regionali o culturali. L'elenco esatto delle lingue per questa funzionalità non è stato pubblicato.

In caso di contenuto espresso in un lingua di uso meno frequente, si può provare la funzionalità Rilevamento lingua per verificare se viene restituito un codice. La risposta per le lingue non rilevabili è `unknown`.

> [!TIP]
> Analisi del testo offre anche un'immagine del contenitore Docker basata su Linux per il rilevamento della lingua, di conseguenza è possibile [installare ed eseguire il contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) vicino ai dati.

## <a name="preparation"></a>Preparazione

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

È necessario disporre di documenti JSON nel formato seguente: ID e testo.

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta. Di seguito è riportato un esempio del contenuto che può essere inviato per il rilevamento della lingua:

```json
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

+ Creare una richiesta POST. Per esaminare la documentazione dell'API per la richiesta, vedere [API Rilevamento lingua](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages).

+ Impostare l'endpoint HTTP per il rilevamento della lingua. Usare una risorsa di Analisi del testo in Azure oppure un [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) di cui è stata creata un'istanza. È necessario includere `/text/analytics/v3.0/languages` nell'URL. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Impostare un'intestazione della richiesta in modo da includere la [chiave di accesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) per le operazioni di Analisi del testo.

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Inviare la richiesta POST

Al momento della ricezione della richiesta viene eseguita l'analisi. Per informazioni sulle dimensioni e sul numero delle richieste che è possibile inviare al minuto e al secondo, vedere la sezione [Limiti dei dati](../overview.md#data-limits) nella panoramica.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.


## <a name="step-3-view-the-results"></a>Passaggio 3: View the results

Tutte le richieste POST restituiscono una risposta in formato JSON con gli ID e le proprietà rilevate.

L'output viene restituito immediatamente. È possibile trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale. Importare quindi l'output in un'applicazione che consente di ordinare, cercare e modificare i dati.

I risultati relativi alla richiesta di esempio dovrebbero avere un aspetto simile al codice JSON seguente. Si noti che si tratta di un singolo documento con più elementi. L'output è in inglese. Gli identificatori di lingua includono un nome descrittivo e un codice di lingua nel formato [ISO 639-1](https://www.iso.org/standard/22109.html).

Un punteggio positivo pari a 1.0 esprime il massimo livello possibile di attendibilità dell'analisi.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "French",
                "iso6391Name": "fr",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "4",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "5",
            "detectedLanguage": {
                "name": "Russian",
                "iso6391Name": "ru",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

### <a name="ambiguous-content"></a>Contenuto ambiguo

In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese/area geografica di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`.

La parola "Impossible", ad esempio, è comune sia alla lingua inglese che alla lingua francese. Se viene specificata con un contesto limitato, la risposta sarà basata sul suggerimento del paese/area geografica "US". Se l'origine del testo è nota come proveniente dalla Francia, è possibile specificare tale paese come suggerimento.

**Input**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

Il contesto aggiuntivo ora disponibile consente al servizio di ottenere un giudizio migliore: 

**Output**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "confidenceScore": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "confidenceScore": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Se l'analizzatore non riesce ad analizzare l'input, restituisce `(Unknown)`. Ciò si verifica ad esempio se si invia un blocco di testo costituito esclusivamente da numeri arabi.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Contenuto in più lingue

Se all'interno dello stesso documento è presente contenuto in più lingue, viene restituita la lingua più rappresentata, ma con una classificazione positiva inferiore, che riflette l'attendibilità marginale della valutazione. Nell'esempio seguente l'input è costituito da una combinazione di inglese, spagnolo e francese. L'analizzatore conta i caratteri in ogni segmento per determinare la lingua prevalente.

**Input**

```json
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

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "confidencescore": 0.94
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Summary

In questo articolo si sono appresi i concetti e il flusso di lavoro per il rilevamento della lingua con Analisi del testo di Servizi cognitivi di Azure. È stato illustrato e dimostrato quanto segue:

+ [Rilevamento lingua](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) è disponibile per un'ampia gamma di lingue, varianti e dialetti e alcune lingue regionali o culturali.
+ I documenti JSON nel corpo della richiesta includono un ID e il testo.
+ La richiesta POST viene inviata a un endpoint `/languages` usando [una chiave di accesso e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizzati validi per la sottoscrizione.
+ L'output di risposta è costituito da identificatori di lingua per ogni ID documento. L'output può essere trasmesso a qualsiasi app che accetta JSON, ad esempio Excel e Power BI, per citarne alcune.

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/text-analytics-sdk.md)
* [Novità](../whats-new.md)
