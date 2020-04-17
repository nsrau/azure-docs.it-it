---
title: Estendere l'app in fase di esecuzione - LUISExtend app at runtime - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538578"
---
# <a name="extend-app-at-prediction-runtime"></a>Estendere l'app in fase di esecuzione di stimaExtend app at prediction runtime

Lo schema dell'app (modelli e funzionalità) viene sottoposto a training e pubblicato nell'endpoint di stima. Questo modello pubblicato viene usato nel runtime di stima. È possibile passare nuove informazioni, insieme all'espressione dell'utente, al runtime di stima per aumentare la stima.

Due modifiche dello schema di runtime di stima includono:Two prediction runtime schema changes include:
* [Entità esterne](#external-entities)
* [Elenchi dinamici](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Entità esterne

Le entità esterne consentono all'app LUIS di identificare ed etichettare le entità durante il runtime, che possono essere usate come funzionalità per le entità esistenti. In questo modo è possibile usare estrattori di entità personalizzati e personalizzati prima di inviare query all'endpoint di stima. Poiché questa operazione viene eseguita nell'endpoint di stima delle query, non è necessario eseguire nuovamente il training e pubblicare il modello.

L'applicazione client fornisce il proprio estrattore di entità gestendo la corrispondenza tra entità e determinando la posizione all'interno dell'espressione dell'entità corrispondente e quindi inviando tali informazioni con la richiesta.

Le entità esterne sono il meccanismo per estendere qualsiasi tipo di entità pur rimanendo utilizzato come segnali ad altri modelli.

Ciò è utile per un'entità con dati disponibili solo in fase di esecuzione di stima delle query. Esempi di questo tipo di dati sono la modifica costante dei dati o di dati specifici per utente. È possibile estendere un'entità contatto LUIS con informazioni esterne dall'elenco contatti di un utente.

Le entità esterne fanno parte dell'API di creazione V3. Ulteriori informazioni sulla [migrazione](luis-migration-api-v3.md) a questa versione.

### <a name="entity-already-exists-in-app"></a>L'entità esiste già nell'app

Il valore `entityName` di per l'entità esterna, passato nel corpo POST della richiesta dell'endpoint, deve essere già presente nell'app sottoposta a training e pubblicata al momento della richiesta. Il tipo di entità non è importante, tutti i tipi sono supportati.

### <a name="first-turn-in-conversation"></a>Primo turno nella conversazione

Si consideri una prima espressione in una conversazione bot di chat in cui un utente immette le informazioni incomplete seguenti:Consider a first utterance in a chat bot conversation where a user enters the following incomplete information:

`Send Hazem a new message`

La richiesta dal bot di chat a LUIS può `Hazem` passare informazioni nel corpo POST su in modo che venga direttamente abbinato come uno dei contatti dell'utente.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

La risposta di stima include tale entità esterna, con tutte le altre entità stimate, perché è definita nella richiesta.

### <a name="second-turn-in-conversation"></a>Secondo turno nella conversazione

La successiva espressione utente nel bot di chat usa un termine più vago:The next user utterance into the chat bot uses a more vague term:

`Send him a calendar reminder for the party.`

In questo turno di conversazione, `him` l'espressione `Hazem`viene utilizzata come riferimento a . Il chat bot conversazionale, nel `him` corpo POST, può eseguire il mapping `Hazem`al valore dell'entità estratto dalla prima espressione, .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

La risposta di stima include tale entità esterna, con tutte le altre entità stimate, perché è definita nella richiesta.

### <a name="override-existing-model-predictions"></a>Eseguire l'override delle stime dei modelli esistentiOverride existing model predictions

La `preferExternalEntities` proprietà options specifica che se l'utente invia un'entità esterna che si sovrappone a un'entità stimata con lo stesso nome, LUIS sceglie l'entità passata o l'entità esistente nel modello.

Si consideri, ad esempio, la query `today I'm free`. LUIS rileva `today` come datetimeV2 con la seguente risposta:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se l'utente invia l'entità esterna:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Se `preferExternalEntities` l'opzione `false`è impostata su , LUIS restituisce una risposta come se l'entità esterna non fosse stata inviata.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se `preferExternalEntities` l'opzione `true`è impostata su , LUIS restituisce una risposta che includa:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Risoluzione

La proprietà _facoltativa_ `resolution` restituisce nella risposta di stima, consentendo di passare i metadati associati all'entità esterna, quindi riceverla nuovamente nella risposta.

Lo scopo principale è estendere le entità predefinite, ma non è limitato a tale tipo di entità.

La `resolution` proprietà può essere un numero, una stringa, un oggetto o una matrice:The property can be an number, an string, an object, or an array:

* "Dallas"
* "testo": "valore"
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Elenchi dinamici

Gli elenchi dinamici consentono di estendere un'entità elenco con training e pubblicato esistente, già presente nell'app LUIS.

Utilizzare questa funzionalità quando i valori dell'entità elenco devono essere modificati periodicamente. Questa funzionalità consente di estendere un'entità elenco già qualificata e pubblicata:This feature allows you to extend an already trained and published list entity:

* Al momento della richiesta dell'endpoint di stima delle query.
* Per una singola richiesta.

L'entità elenco può essere vuota nell'app LUIS, ma deve esistere. L'entità elenco nell'app LUIS non viene modificata, ma la capacità di stima nell'endpoint viene estesa per includere fino a 2 elenchi con circa 1.000 elementi.

### <a name="dynamic-list-json-request-body"></a>Corpo della richiesta JSON dell'elenco dinamico

Inviare il corpo JSON seguente per aggiungere un nuovo sottoelenco con sinonimi all'elenco e stimare l'entità dell'elenco per il testo `LUIS`, con la richiesta di stima della `POST` query:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

La risposta di stima include tale entità elenco, con tutte le altre entità stimate, perché è definita nella richiesta.

## <a name="next-steps"></a>Passaggi successivi

* [Punteggio di previsione](luis-concept-prediction-score.md)
* [Modifica dell'API di creazione V3](luis-migration-api-v3.md)
