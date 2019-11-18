---
title: Abilità dell'API Web personalizzata in skillsets
titleSuffix: Azure Cognitive Search
description: Estendi le funzionalità di Azure ricerca cognitiva skillsets chiamando le API Web. Usare la competenza API Web personalizzata per integrare il codice personalizzato.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: defe6711049e191ada1a2f6e46d6643debdca86e
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113806"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Abilità dell'API Web personalizzata in una pipeline di arricchimento ricerca cognitiva di Azure

La competenza dell' **API Web personalizzata** consente di estendere l'arricchimento di intelligenza artificiale chiamando un endpoint API Web che fornisce operazioni personalizzate. Analogamente alle competenze predefinite, una competenza **API Web personalizzata** ha input e output. A seconda degli input, l'API Web riceve un payload JSON quando l'indicizzatore viene eseguito e restituisce un payload JSON come risposta, insieme a un codice di stato di esito positivo. È previsto che la risposta abbia gli output specificati dalla competenza personalizzata. Qualsiasi altra risposta è considerata un errore e non vengono eseguiti arricchimenti.

La struttura dei payload JSON è descritta in dettaglio più avanti in questo documento.

> [!NOTE]
> L'indicizzatore riproverà due volte per determinati codici di stato HTTP standard restituiti dall'API Web. Questi codici di stato HTTP sono: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| Uri | URI dell'API Web a cui verrà inviato il payload _JSON_ . È consentito solo lo schema URI **https** |
| httpMethod | Metodo da usare per l'invio del payload. I metodi consentiti sono `PUT` o `POST` |
| httpHeaders | Raccolta di coppie chiave-valore in cui le chiavi corrispondono ai nomi di intestazione e i valori rappresentano i valori di intestazione che verranno inviati all'API Web insieme al payload. In questa raccolta è proibito l'uso delle intestazioni seguenti: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | (facoltativo) Se specificato, indica il timeout per il client HTTP che effettua la chiamata API. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Ad esempio, `PT60S` per 60 secondi. Se non impostato, viene scelto un valore predefinito di 30 secondi. Il timeout può essere impostato su un massimo di 230 secondi e un minimo di 1 secondo. |
| batchSize | (facoltativo) Indica quanti "record di dati" (vedere la struttura del payload _JSON_ più avanti) verranno inviati per ogni chiamata API. Se non impostato, viene scelto un valore predefinito di 1000. È consigliabile usare questo parametro per ottenere un compromesso accettabile tra velocità effettiva di indicizzazione e carico sull'API |
| degreeOfParallelism | Opzionale Quando specificato, indica il numero di chiamate che l'indicizzatore farà in parallelo all'endpoint fornito. È possibile ridurre questo valore se l'endpoint ha un errore troppo elevato di un carico di richiesta o se l'endpoint è in grado di accettare più richieste e si desidera un aumento delle prestazioni dell'indicizzatore.  Se non impostato, viene utilizzato il valore predefinito 5. DegreeOfParallelism può essere impostato su un massimo di 10 e un minimo di 1. |

## <a name="skill-inputs"></a>Input competenze

Non ci sono input predefiniti per questa competenza. È possibile scegliere uno o più campi che sarebbero già disponibili al momento dell'esecuzione della competenza come input e il payload_JSON_ inviato all'API Web avrà campi diversi.

## <a name="skill-outputs"></a>Output competenze

Non ci sono output predefiniti per questa competenza. A seconda della risposta restituita dall'API Web, aggiungere campi di output in modo che possano essere prelevati dalla risposta _JSON_.


## <a name="sample-definition"></a>Definizione di esempio

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Struttura JSON di input di esempio

La struttura _JSON_ rappresenta il payload che verrà inviato all'API Web.
Seguirà sempre questi vincoli:

* L'entità di primo livello è denominata `values` e sarà una matrice di oggetti. Il numero di tali oggetti corrisponderà al massimo a `batchSize`
* Ogni oggetto nella matrice `values` avrà
    * Una proprietà `recordId` che è una stringa **univoca** usata per identificare tale record.
    * Una proprietà `data` che è un oggetto _JSON_. I campi della proprietà `data` corrisponderanno ai "nomi" specificati nella sezione `inputs` della definizione della competenza. Il valore di questi campi verrà da `source` (che può essere un campo nel documento o potenzialmente un'altra competenza)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Struttura JSON di output di esempio

L'output corrisponde alla risposta restituita dall'API Web. L'API Web deve restituire solo un payload _JSON_ (verificato osservando l'intestazione della risposta `Content-Type`) e deve soddisfare i vincoli seguenti:

* Deve contenere un'entità di primo livello denominata `values` che deve essere una matrice di oggetti.
* Il numero di oggetti nella matrice deve essere uguale al numero di oggetti inviati all'API Web.
* Ogni oggetto deve avere:
   * Una proprietà `recordId`
   * Una proprietà `data`, che è un oggetto in cui i campi sono arricchimenti corrispondenti ai "nomi" nell'`output` e il cui valore viene considerato l'arricchimento.
   * Una proprietà `errors`, una matrice che elenca tutti gli errori rilevati che verranno aggiunti alla cronologia di esecuzione dell'indicizzatore. Questa proprietà è obbligatoria, ma può avere un valore `null`.
   * Una proprietà `warnings`, una matrice che elenca tutti gli avvisi rilevati che verranno aggiunti alla cronologia di esecuzione dell'indicizzatore. Questa proprietà è obbligatoria, ma può avere un valore `null`.
* Gli oggetti nella matrice `values` non devono essere nello stesso ordine degli oggetti nella matrice `values` inviata come richiesta all'API Web. Tuttavia, il valore `recordId` viene usato per la correlazione, di conseguenza eventuali record nella risposta contenenti un `recordId` che non fa parte della richiesta originale all'API Web verranno rimossi.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Casi di errore
Oltre alla non disponibilità dell'API Web o all'invio di codici di stato che non indicano un esito positivo, sono considerati casi di errore i seguenti:

* Se l'API Web restituisce un codice di stato di esito positivo, ma la risposta indica che non è `application/json`, la risposta viene considerata non valida e non verranno eseguiti arricchimenti.
* Se sono presenti record **non validi** (con `recordId` non nella richiesta originale o con valori duplicati) nella matrice `values` della risposta, per **quei** record non verranno eseguiti arricchimenti.

Nei casi in cui l'API Web non è disponibile o restituisce un errore HTTP, un errore descrittivo con i dettagli disponibili sull'errore HTTP verrà aggiunto alla cronologia di esecuzione dell'indicizzatore.

## <a name="see-also"></a>Vedere anche

+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [Aggiungere competenze personalizzate a una pipeline di arricchimento di intelligenza artificiale](cognitive-search-custom-skill-interface.md)
+ [Esempio: creazione di un'abilità personalizzata per l'arricchimento di intelligenza artificiale (cognitive-ricerca-creazione-personalizzata-skill-example.md)