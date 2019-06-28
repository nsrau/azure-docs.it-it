---
title: Migrazione da API V3 v2
titleSuffix: Azure Cognitive Services
description: L'endpoint della versione 3 sono state modificate le API. Usare questa guida per comprendere come eseguire la migrazione alla API dell'endpoint versione 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 4c08c95a05d4f22e2338a7264409aec0f64a4755
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442510"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Anteprima: Eseguire la migrazione alla versione dell'API 3.x per le app LUIS

L'endpoint di stima di query sono state modificate le API. Usare questa guida per comprendere come eseguire la migrazione alla API dell'endpoint versione 3. 

Questa API V3 offre le nuove funzionalità seguenti, che includono modifiche di richiesta e/o risposta JSON significative: 

* [Entità esterne](#external-entities-passed-in-at-prediction-time)
* [Elenchi dinamici](#dynamic-lists-passed-in-at-prediction-time)
* [Modifiche JSON entità predefiniti](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

L'endpoint di query prediction [richiesta](#request-changes) e [risposta](#response-changes) presentano modifiche significative per supportare le nuove funzionalità elencate in precedenza, inclusi i seguenti:

* [Modifiche all'oggetto risposta](#top-level-json-changes)
* [Riferimenti al nome ruolo entità anziché il nome di entità](#entity-role-name-instead-of-entity-name)
* [Proprietà consentono di contrassegnare entità nelle espressioni](#marking-placement-of-entities-in-utterances)

Sono le seguenti funzionalità di LUIS **non è supportato** nell'API V3:

* V7 di controllo ortografico Bing

[Documentazione di riferimento](https://aka.ms/luis-api-v3) è disponibile per V3.

## <a name="endpoint-url-changes-by-slot-name"></a>Modifiche all'endpoint URL in base al nome dello slot

Il formato della chiamata dell'endpoint HTTP V3 è cambiato.

|(METODO)|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

## <a name="endpoint-url-changes-by-version-id"></a>Modifiche all'URL endpoint base all'ID versione

Se si desidera eseguire una query dalla versione, è innanzitutto necessario [pubblicare tramite l'API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) con il `"directVersionPublish":true`. Query sull'endpoint fa riferimento all'ID versione anziché il nome dello slot.


|(METODO)|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Entità predefinite con nuovo JSON

Includono le modifiche all'oggetto risposta V3 [entità predefinite](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Richiedere modifiche 

### <a name="query-string-parameters"></a>Parametri della stringa di query

L'API V3 include parametri della stringa di query diversi.

|Nome del parametro|Type|Version|Predefinito|Scopo|
|--|--|--|--|--|
|`log`|boolean|V2 E V3|false|Query Store nel file di log.| 
|`query`|string|Solo V3|Nessuna impostazione predefinita: è obbligatorio nella richiesta GET|**Nella versione 2**, si trova il utterance deve essere stimata nel `q` parametro. <br><br>**In V3**, la funzionalità viene passata il `query` parametro.|
|`show-all-intents`|boolean|Solo V3|false|Restituire tutti i tipi con il punteggio corrispondente nel **prediction.intents** oggetto. Gli Intent vengono restituiti come oggetti in un elemento padre `intents` oggetto. Ciò consente l'accesso a livello di codice senza la necessità di individuare lo scopo in una matrice: `prediction.intents.give`. Nella versione 2, questi sono stati restituiti in una matrice. |
|`verbose`|boolean|V2 E V3|false|**Nella versione V2**, quando impostato su true, tutti stimato Intent sono stati restituiti. Se è necessario stimato tutti gli Intent, usare il parametro V3 di `show-all-intents`.<br><br>**In V3**, questo parametro fornisce solo entità i dettagli della stima di entità dei metadati.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>La stima di query JSON del corpo per il `POST` richiesta

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Proprietà|Type|Version|Predefinito|Scopo|
|--|--|--|--|--|
|`dynamicLists`|array|Solo V3|Non obbligatorio.|[Gli elenchi dinamici](#dynamic-lists-passed-in-at-prediction-time) consentono di estendere un'entità sottoposto a training e pubblicato un elenco esistente, già nell'app LUIS.|
|`externalEntities`|array|Solo V3|Non obbligatorio.|[Le entità esterne](#external-entities-passed-in-at-prediction-time) assegnare all'app LUIS la possibilità di identificare e classificare le entità in fase di esecuzione, che possa essere usate come funzionalità alle entità esistenti. |
|`options.datetimeReference`|string|Solo V3|Nessun valore predefinito|Consente di determinare [datetimeV2 offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity).|
|`options.overridePredictions`|boolean|Solo V3|false|Specifica se dell'utente [entità esterne (con stesso nome di entità esistente)](#override-existing-model-predictions) viene usato o l'entità esistente nel modello viene utilizzato per la stima. |
|`query`|string|Solo V3|Richiesto.|**Nella versione 2**, si trova il utterance deve essere stimata nel `q` parametro. <br><br>**In V3**, la funzionalità viene passata il `query` parametro.|



## <a name="response-changes"></a>Modifiche di risposta

La risposta alla query JSON modificata per consentire l'accesso a livello di codice maggiore ai dati usati più di frequente. 

### <a name="top-level-json-changes"></a>Modifiche a livello superiore di JSON

Le proprietà JSON principali per la versione V2 sono, quando `verbose` è impostata su true, che restituisce tutti i tipi e i punteggi del `intents` proprietà:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Le prime proprietà JSON per V3 sono:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

Il `intents` oggetto è un elenco non ordinato. Non presupporre che il primo elemento figlio nel `intents` corrisponde alla `topIntent`. Usare invece il `topIntent` valore da trovare il punteggio:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Consentono le modifiche dello schema di risposta JSON:

* Distinzione tra utterance originale, deselezionare `query`e ha restituito stima, `prediction`.
* Più facile accesso programmatico a dati stimati. Invece di enumerare una matrice nella versione 2, è possibile accedere ai valori da **denominato** per le finalità ed entità. Per i ruoli di entità previsto, viene restituito il nome di ruolo perché tale valore è univoco per l'intera app.
* Tipi di dati, se determinato, vengono rispettati. Dati numerici non sono più vengono restituiti come stringhe.
* Distinguere tra la prime informazioni di stima di priorità e i metadati aggiuntivi, restituito nel `$instance` oggetto. 

### <a name="access-instance-for-entity-metadata"></a>Accesso `$instance` per i metadati dell'entità

Se è necessario metadati delle entità, la stringa di query deve usare la `verbose=true` flag e la risposta contiene i metadati nel `$instance` oggetto. Nelle risposte JSON nelle sezioni seguenti sono riportati alcuni esempi.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Ogni entità stimato è rappresentato come una matrice

Il `prediction.entities.<entity-name>` oggetto contiene una matrice perché ogni entità può essere stimato più volte nel utterance. 

### <a name="list-entity-prediction-changes"></a>Modifiche all'elenco entità stima

Il codice JSON per una stima di entità di elenco è stato modificato per essere una matrice di matrici:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Ogni matrice interna corrisponde al testo all'interno di utterance. L'oggetto interno è una matrice perché lo stesso testo può apparire in più di un sottoelenco di un'entità di elenco. 

Quando il mapping tra il `entities` dell'oggetto per il `$instance` dell'oggetto, l'ordine degli oggetti viene mantenuto per le stime di entità di elenco.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Nome del ruolo entità anziché il nome di entità 

Nella versione 2, il `entities` matrice restituita di tutte le entità stimate con il nome dell'entità in corso l'identificatore univoco. In V3, se l'entità vengono utilizzati i ruoli e la stima sia per un ruolo di entità, l'identificatore primario è il nome del ruolo. Questo è possibile perché i nomi dei ruoli di entità deve essere univoci per l'intera app tra cui gli altri nomi di modello (entity preventivi,).

Nell'esempio seguente: prendere in considerazione un utterance che include i testo `Yellow Bird Lane`. Questo testo viene stimato come proprietà personalizzata `Location` ruolo dell'entità di `Destination`.

|Testo utterance|Nome dell'entità|Nome del ruolo|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Nella versione 2, l'entità è identificata dal _nome dell'entità_ con il ruolo come una proprietà dell'oggetto:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

In V3, l'entità fa riferimento il _ruolo dell'entità_, se la stima sia per il ruolo:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

In V3, lo stesso risultato con il `verbose` flag per restituire i metadati dell'entità:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Le entità esterne passata in fase di stima

Le entità esterne assegnare all'app LUIS la possibilità di identificare e classificare le entità in fase di esecuzione, che possa essere usate come funzionalità alle entità esistenti. Ciò consente di usare estrattori personalizzati entità separate e personalizzate prima di inviare query all'endpoint della stima. Poiché questa operazione viene eseguita in corrispondenza dell'endpoint di stima di query, non è necessario ripetere il training e pubblicare il modello.

L'applicazione client consiste nel fornire un proprio estrattore di entità mediante la gestione di entità corrispondenti e determinare la posizione all'interno di utterance di tale entità corrispondente e quindi l'invio di tali informazioni con la richiesta. 

Le entità esterne sono il meccanismo per estendere qualsiasi tipo di entità mentre ancora in uso come segnali e altri modelli come i ruoli, composite e altri.

Ciò è utile per un'entità che dispone di dati disponibili solo in fase di esecuzione di query stima. Esempi di questo tipo di dati cambiano continuamente i dati o specifici per ogni utente. È possibile estendere un'entità contatto LUIS con informazioni esterne dall'elenco di contatti dell'utente. 

### <a name="entity-already-exists-in-app"></a>Entità esiste già nell'app

Il valore di `entityName` per l'entità esterna, passato nel corpo del POST, la richiesta dell'endpoint deve essere già presenti nell'app pubblicata e sottoposto a training al momento della richiesta. Il tipo di entità non è rilevante, tutti i tipi sono supportati.

### <a name="first-turn-in-conversation"></a>Prima di tutto convertire in conversazione

Si consideri un primo utterance nelle conversazioni chat bot in cui un utente immette le informazioni incomplete seguenti:

`Send Hazem a new message`

La richiesta di bot chat di LUIS può passare informazioni nel corpo del POST su `Hazem` in modo che viene confrontato direttamente come uno dei contatti dell'utente.

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

La risposta di stima include tale entità esterna, con tutte le altre stimate entità, perché è definito nella richiesta.  

### <a name="second-turn-in-conversation"></a>In secondo luogo attiva in conversazione

Il successivo utterance utente nella finestra di chatbot Usa un termine più vaga:

`Send him a calendar reminder for the party.`

In utterance precedente, viene utilizzato il utterance `him` come riferimento a `Hazem`. Bot, chat discorsiva nel corpo del POST, è possibile mappare `him` sul valore di entità estratto da utterance prima, `Hazem`.

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

La risposta di stima include tale entità esterna, con tutte le altre stimate entità, perché è definito nella richiesta.  

### <a name="override-existing-model-predictions"></a>Eseguire l'override delle stime del modello esistente

Il `overridePredictions` proprietà di opzioni consente di specificare che, se l'utente invia un'entità esterna che si sovrappone con un'entità stimata con lo stesso nome, LUIS sceglie l'entità passata o l'entità esistente nel modello. 

Ad esempio, si consideri la query `today I'm free`. LUIS rileva `today` come un datetimeV2 con la risposta seguente:

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

Se l'utente invia le entità esterne:

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

Se il `overridePredictions` è impostata su `false`, LUIS restituisce una risposta come se l'entità esterna non sono stati inviati. 

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

Se il `overridePredictions` è impostata su `true`, LUIS restituisce una risposta che include:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Risoluzione

Il _facoltativi_ `resolution` restituisce proprietà nella risposta stima, consentendo di passare i metadati associati all'entità esterna, quindi lo riceveranno nuovamente nella risposta. 

Lo scopo principale è per estendere le entità predefinite, ma non è limitato a tale tipo di entità. 

Il `resolution` proprietà può essere un numero, stringa, un oggetto o una matrice:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Gli elenchi dinamici passata in fase di stima

Gli elenchi dinamici consentono di estendere un'entità sottoposto a training e pubblicato un elenco esistente, già nell'app LUIS. 

Utilizzare questa funzionalità quando è necessario modificare periodicamente i valori di entità elenco. Questa funzionalità consente di estendere un'entità già sottoposto a training e pubblicato un elenco:

* Al momento della richiesta dell'endpoint di stima di query.
* Per una singola richiesta.

L'entità elenco può essere vuoto nell'app LUIS ma presenta a esistere. Non viene modificato l'entità elenco nell'app LUIS, ma la capacità di stima in corrispondenza dell'endpoint è stato esteso per includere fino a 2 elenchi con circa 1.000 elementi.

### <a name="dynamic-list-json-request-body"></a>Elenco dinamico di corpo della richiesta JSON

Inviare nel corpo JSON seguente per aggiungere un nuovo sottoelenco con sinonimi all'elenco e stimare l'entità di elenco per il testo `LUIS`, con la `POST` richiesta di stima di query:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
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

La risposta di stima include entità elenco, con tutte le altre stimate entità, perché è definito nella richiesta. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset rinominata datetimeReference

**Nella versione 2**, il `timezoneOffset` [parametro](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) viene inviato nella richiesta di stima come parametro di stringa di query, indipendentemente dal fatto che se la richiesta viene inviata una richiesta GET o POST. 

**In V3**, la stessa funzionalità viene fornita con il parametro del corpo di POST `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Contrassegno di selezione host delle entità in espressioni

**Nella versione 2**, un'entità è stata contrassegnata in un utterance con il `startIndex` e `endIndex`. 

**In V3**, l'entità è contrassegnato con `startIndex` e `entityLength`.

## <a name="deprecation"></a>Deprecazione 

L'API V2 verrà deprecata non di almeno 9 mesi dopo l'anteprima di V3. 

## <a name="next-steps"></a>Passaggi successivi

Usare la documentazione dell'API V3 REST esistenti di aggiornare le chiamate a LUIS [endpoint](https://aka.ms/luis-api-v3) API. 