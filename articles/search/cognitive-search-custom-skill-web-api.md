---
title: Competenza personalizzata della ricerca cognitiva - Ricerca di Azure
description: Estendere le funzionalità dei set di competenze di ricerca cognitiva chiamando API Web
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: e5f7ee172563a81d45e3a35da2cfc7e8731de48d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023864"
---
# <a name="custom-web-api-skill"></a>Competenza API Web personalizzata

Il **API Web personalizzata** competenze consente di estendere ricerca cognitiva chiamando un endpoint dell'API Web che fornisce operazioni personalizzate. Analogamente alle competenze predefinite, una competenza **API Web personalizzata** ha input e output. In base agli input, l'API Web riceve un payload JSON durante le esecuzioni dell'indicizzatore e restituisce un payload JSON sotto forma di risposta, con un codice di stato di esito positivo. È previsto che la risposta abbia gli output specificati dalla competenza personalizzata. Qualsiasi altra risposta è considerata un errore e non vengono eseguiti arricchimenti.

La struttura dei payload JSON è descritta in dettaglio più avanti in questo documento.

> [!NOTE]
> L'indicizzatore riproverà due volte per determinati codici di stato HTTP standard restituiti dall'API Web. Questi codici di stato HTTP sono: 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| Uri | L'URI dell'API Web a cui il _JSON_ payload verrà inviato. È consentito solo lo schema URI **https** |
| httpMethod | Metodo da usare per l'invio del payload. I metodi consentiti sono `PUT` o `POST` |
| httpHeaders | Raccolta di coppie chiave-valore in cui le chiavi corrispondono ai nomi di intestazione e i valori rappresentano i valori di intestazione che verranno inviati all'API Web insieme al payload. In questa raccolta è proibito l'uso delle intestazioni seguenti: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | (facoltativo) Se specificato, indica il timeout per il client HTTP che effettua la chiamata API. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Ad esempio, `PT60S` per 60 secondi. Se non impostato, viene scelto un valore predefinito di 30 secondi. Il timeout può essere impostato su un massimo di 90 secondi e un minimo di 1 secondo. |
| batchSize | (facoltativo) Indica quanti "record di dati" (vedere la struttura del payload _JSON_ più avanti) verranno inviati per ogni chiamata API. Se non impostato, viene scelto un valore predefinito di 1000. È consigliabile usare questo parametro per ottenere un compromesso accettabile tra velocità effettiva di indicizzazione e carico sull'API |

## <a name="skill-inputs"></a>Input competenze

Non ci sono input predefiniti per questa competenza. È possibile scegliere uno o più campi che sarebbero già disponibili al momento dell'esecuzione della competenza come input e il payload_JSON_ inviato all'API Web avrà campi diversi.

## <a name="skill-outputs"></a>Output competenze

Non ci sono output predefiniti per questa competenza. A seconda della risposta restituita dall'API Web, aggiungere campi di output in modo che possano essere prelevati dalla risposta _JSON_.


## <a name="sample-definition"></a>Definizione di esempio

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
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
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
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
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Struttura JSON di output di esempio

Il "output" corrisponde alla risposta restituita dall'API Web. L'API Web deve restituire solo un _JSON_ payload (verificato esaminando il `Content-Type` intestazione della risposta) e devono soddisfare i vincoli seguenti:

* Deve contenere un'entità di primo livello denominata `values` che deve essere una matrice di oggetti.
* Il numero di oggetti nella matrice deve essere lo stesso come il numero di oggetti inviate all'API Web.
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
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
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

+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come aggiungere una competenza personalizzata a una pipeline di ricerca cognitiva](cognitive-search-custom-skill-interface.md)
+ [Creare una competenza personalizzata usando l'API Traduzione testuale](cognitive-search-create-custom-skill-example.md)