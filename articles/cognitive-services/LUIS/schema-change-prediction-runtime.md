---
title: Estendi app in fase di esecuzione-LUIS
description: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 69e2608fb01ece81f555aae2f3d4a2e4a05cfc90
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322802"
---
# <a name="extend-app-at-prediction-runtime"></a>Estendi app a runtime di stima

Lo schema dell'app (modelli e funzionalità) viene sottoposto a training e pubblicato nell'endpoint di stima. Questo modello pubblicato viene utilizzato nel runtime di stima. Per aumentare la stima, è possibile passare le nuove informazioni, insieme all'espressione dell'utente, al runtime di stima.

Due modifiche allo schema del runtime di stima includono:
* [Entità esterne](#external-entities)
* [Elenchi dinamici](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Entità esterne

Le entità esterne offrono all'app LUIS la possibilità di identificare ed etichettare entità durante il runtime, che possono essere usate come funzionalità per le entità esistenti. In questo modo è possibile utilizzare estrattiri di entità distinti e personalizzati prima di inviare query all'endpoint di stima. Poiché questa operazione viene eseguita nell'endpoint di stima della query, non è necessario ripetere il training e pubblicare il modello.

L'applicazione client fornisce il proprio estrattore di entità gestendo la corrispondenza delle entità e determinando la posizione nell'espressione dell'entità corrispondente e quindi inviando tali informazioni con la richiesta.

Le entità esterne sono il meccanismo per l'estensione di qualsiasi tipo di entità, pur continuando a essere utilizzato come segnali per altri modelli.

Questa operazione è utile per un'entità che dispone di dati disponibili solo nel runtime di stima query. Esempi di questo tipo di dati sono dati in costante evoluzione o specifici per utente. È possibile estendere un'entità Contact LUIS con informazioni esterne dall'elenco dei contatti di un utente.

Le entità esterne fanno parte dell'API di creazione V3. Altre informazioni sulla [migrazione](luis-migration-api-v3.md) a questa versione.

### <a name="entity-already-exists-in-app"></a>L'entità esiste già nell'app

Il valore di `entityName` per l'entità esterna, passato nel corpo della richiesta dell'endpoint, deve esistere già nell'app SOTTOposta a training e pubblicata al momento della richiesta. Il tipo di entità non è rilevante, sono supportati tutti i tipi.

### <a name="first-turn-in-conversation"></a>Primo turno di conversazione

Si consideri un primo enunciato in una conversazione bot chat in cui un utente immette le informazioni incomplete seguenti:

`Send Hazem a new message`

La richiesta dal bot della chat a LUIS può passare informazioni sul corpo del POST, in `Hazem` modo che corrisponda direttamente a uno dei contatti dell'utente.

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

La risposta di stima include l'entità esterna, con tutte le altre entità stimate, perché è definita nella richiesta.

### <a name="second-turn-in-conversation"></a>Seconda attivazione della conversazione

Il successivo enunciato dell'utente in chat bot usa un termine più vago:

`Send him a calendar reminder for the party.`

In questo turno della conversazione, l'espressione USA `him` come riferimento a `Hazem` . Il bot della chat conversazione, nel corpo POST, può eseguire `him` il mapping al valore dell'entità Estratto dal primo enunciato, `Hazem` .

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

La risposta di stima include l'entità esterna, con tutte le altre entità stimate, perché è definita nella richiesta.

### <a name="override-existing-model-predictions"></a>Eseguire l'override delle stime del modello esistenti

La `preferExternalEntities` proprietà Options specifica che se l'utente invia un'entità esterna che si sovrappone a un'entità stimata con lo stesso nome, Luis sceglie l'entità passata o l'entità esistente nel modello.

Si consideri, ad esempio, la query `today I'm free`. LUIS rileva `today` come datetimeV2 con la risposta seguente:

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

Se `preferExternalEntities` è impostato su `false` , Luis restituisce una risposta come se l'entità esterna non venisse inviata.

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

Se `preferExternalEntities` è impostato su `true` , Luis restituisce una risposta che include:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Soluzione

La proprietà _facoltativa_ `resolution` restituisce nella risposta di stima, consentendo di passare i metadati associati all'entità esterna, quindi di riceverli di nuovo nella risposta.

Lo scopo principale consiste nell'estendere le entità predefinite, ma questo non è limitato a tale tipo di entità.

La `resolution` proprietà può essere un numero, una stringa, un oggetto o una matrice:

* Dallas
* {"Text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Elenchi dinamici

Gli elenchi dinamici consentono di estendere un'entità di elenco con training e pubblicato esistente, già nell'app LUIS.

Usare questa funzionalità quando i valori dell'entità elenco devono essere modificati periodicamente. Questa funzionalità consente di estendere un'entità di elenco già sottoposta a training e pubblicato:

* Al momento della richiesta dell'endpoint di stima della query.
* Per una singola richiesta.

L'entità List può essere vuota nell'app LUIS, ma deve esistere. L'entità List nell'app LUIS non è stata modificata, ma la capacità di stima all'endpoint viene estesa per includere fino a 2 elenchi con circa 1.000 elementi.

### <a name="dynamic-list-json-request-body"></a>Corpo della richiesta JSON dell'elenco dinamico

Inviare il corpo JSON seguente per aggiungere un nuovo sottoelenco con sinonimi all'elenco e stimare l'entità list per il testo, `LUIS` , con la `POST` richiesta di stima della query:

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

La risposta di stima include l'entità elenco, con tutte le altre entità stimate, perché è definita nella richiesta.

## <a name="next-steps"></a>Passaggi successivi

* [Punteggio di previsione](luis-concept-prediction-score.md)
* [Modifiche dell'API V3 per la creazione](luis-migration-api-v3.md)
