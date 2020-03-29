---
title: Modifiche dell'endpoint di stima nell'API V3Prediction endpoint changes in the V3 API
description: L'endpoint di stima delle query V3 API sono state modificate. Usare questa guida per informazioni su come eseguire la migrazione alle API degli endpoint versione 3.Use this guide to understand how to migrate to version 3 endpoint APIs.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117380"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Modifiche all'endpoint di stima per V3Prediction endpoint changes for V3

L'endpoint di stima delle query V3 API sono state modificate. Usare questa guida per informazioni su come eseguire la migrazione alle API degli endpoint versione 3.Use this guide to understand how to migrate to version 3 endpoint APIs.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Stato generalmente disponibile:** questa API V3 include modifiche significative di richiesta e risposta JSON dall'API V2.

L'API V3 offre le seguenti nuove funzionalità:

* [Entità esterne](#external-entities-passed-in-at-prediction-time)
* [Elenchi dinamici](#dynamic-lists-passed-in-at-prediction-time)
* [Modifiche JSON dell'entità predefinita](#prebuilt-entity-changes)

La [richiesta](#request-changes) e la [risposta](#response-changes) dell'endpoint di stima presentano modifiche significative per supportare le nuove funzionalità elencate in precedenza, tra cui le seguenti:

* [Modifiche all'oggetto Response](#top-level-json-changes)
* [Riferimenti al nome del ruolo entità anziché al nome dell'entità](#entity-role-name-instead-of-entity-name)
* [Proprietà per contrassegnare le entità nelle espressioniProperties to mark entities in utterances](#marking-placement-of-entities-in-utterances)

[La documentazione](https://aka.ms/luis-api-v3) di riferimento è disponibile per V3.

## <a name="v3-changes-from-preview-to-ga"></a>Modifiche V3 dall'anteprima a GA

V3 ha apportato le seguenti modifiche come parte del passaggio a GA:

* Le entità predefinite seguenti hanno risposte JSON diverse:The following prebuilt entities have different JSON responses:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Nome chiave unità `units` misurabile da a`unit`

* Modifica JSON del corpo della richiesta:Request body JSON change:
    * da `preferExternalEntities` a`preferExternalEntities`
    * parametro facoltativo `score` per le entità esterne

* Modifiche JSON del corpo della risposta:Response body JSON changes:
    * `normalizedQuery`Rimosso

## <a name="suggested-adoption-strategy"></a>Strategia di adozione suggerita

Se si usa Bot Framework, Controllo ortografico di Bing V7 o si desidera eseguire la migrazione solo della creazione di app LUIS, continuare a usare l'endpoint V2.

Se non si conosce l'applicazione client o le integrazioni (Bot Framework e Controllo ortografico Bing V7) e si ha familiarità con la migrazione della creazione dell'app LUIS e dell'endpoint di stima contemporaneamente, iniziare a usare l'endpoint di stima V3. L'endpoint di stima V2 sarà ancora disponibile ed è una buona strategia di ricaduta.


## <a name="not-supported"></a>Non supportate

### <a name="bing-spell-check"></a>Controllo ortografico Bing

Questa API non è supportata nell'endpoint di stima V3: continuare a usare l'endpoint di stima dell'API V2 per le correzioni ortografiche. Se è necessaria la correzione ortografica durante l'utilizzo dell'API V3, chiedere all'applicazione client di chiamare l'API controllo [ortografico Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) e modificare il testo con l'ortografia corretta, prima di inviare il testo all'API LUIS.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Applicazioni client bot Framework e servizio bot di AzureBot Framework and Azure Bot Service client applications

Continuare a usare l'endpoint di previsione dell'API V2 finché non viene rilasciato il V4.7 di Bot Framework.

## <a name="v2-api-deprecation"></a>Deprecazione dell'API V2

L'API di stima V2 non sarà deprecata per almeno 9 mesi dopo l'anteprima V3, 8 giugno 2020.

## <a name="endpoint-url-changes"></a>Modifiche all'URL dell'endpointEndpoint URL changes

### <a name="changes-by-slot-name-and-version-name"></a>Modifiche in base al nome dello slot e al nome della versione

Il formato della chiamata HTTP dell'endpoint V3 è stato modificato.

Se si desidera eseguire una query in base `"directVersionPublish":true`alla versione, è innanzitutto necessario [pubblicare tramite API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) con . Eseguire una query sull'endpoint che fa riferimento all'ID versione anziché al nome dello slot.

|VERSIONE API DI STIMA|METHOD|URL|
|--|--|--|
|V3|GET|https://<b>'REGION api.cognitive.microsoft.com/luis/'</b><b>previsione</b>/<b>v3.0</b>/apps/<b>/slots/</b>/slots/<b>/slots/ /NOME-SLOT /</b>predict?query<b>{QUERY}</b>|
|V3|POST|https://<b>.REGION'</b>api.cognitive.microsoft.com/luis/<b>previsione</b>/<b>v3.0</b>/apps/<b>/APP-ID /</b>slots/<b>/SLOT-NAME /predict</b>|
|V2|GET|https://<b>'REGION'</b><b>.api.cognitive.microsoft.com/luis/ stima</b>/<b>v3.0</b>/apps/<b>/APP-ID /versions/</b>/versions/<b>'VERSION-ID'</b>/predict?query'<b>{QUERY}</b>|
|V2|POST|https://<b>.REGION api.cognitive.microsoft.com/luis/:</b><b>stima</b>/<b>v3.0</b>/apps/<b>/APP-ID /</b>versions/<b>/VERSION-ID /predict</b>|

|Valori validi per`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Richiedere modifiche

### <a name="query-string-changes"></a>Modifiche alle stringhe di query

L'API V3 ha diversi parametri di stringa di query.

|Nome param|Type|Versione|Predefinito|Scopo|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Archiviare query nel file di registro. Il valore predefinito è False.|
|`query`|string|Solo V3|Nessun valore predefinito: è necessario nella richiesta GET|**In V2**, l'espressione da `q` stimare è nel parametro . <br><br>**In V3**, la funzionalità `query` viene passata nel parametro .|
|`show-all-intents`|boolean|Solo V3|false|Restituisce tutte le finalità con il punteggio corrispondente nell'oggetto **prediction.intents.** Le finalità vengono restituite `intents` come oggetti in un oggetto padre. Ciò consente l'accesso a livello di codice `prediction.intents.give`senza la necessità di trovare la finalità in una matrice: . In V2, questi sono stati restituiti in un array. |
|`verbose`|boolean|V2 & V3|false|**In V2**, quando è impostato su true, tutte le finalità previste sono state restituite. Se sono necessarie tutte le finalità previste, utilizzare il parametro V3 di `show-all-intents`.<br><br>**In V3**questo parametro fornisce solo i dettagli dei metadati di entità della stima dell'entità.  |
|`timezoneOffset`|string|V2|-|Fuso orario applicato alle entità datetimeV2.|
|`datetimeReference`|string|V3|-|[Fuso orario](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) applicato alle entità datetimeV2. Sostituisce `timezoneOffset` da V2.|


### <a name="v3-post-body"></a>Corpo V3 POST

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Proprietà|Type|Versione|Predefinito|Scopo|
|--|--|--|--|--|
|`dynamicLists`|array|Solo V3|Non obbligatorio.|[Gli elenchi dinamici](#dynamic-lists-passed-in-at-prediction-time) consentono di estendere un'entità elenco con training e pubblicato esistente, già presente nell'app LUIS.|
|`externalEntities`|array|Solo V3|Non obbligatorio.|[Le entità esterne](#external-entities-passed-in-at-prediction-time) consentono all'app LUIS di identificare ed etichettare le entità durante il runtime, che possono essere usate come funzionalità per le entità esistenti. |
|`options.datetimeReference`|string|Solo V3|Nessun valore predefinito|Utilizzato per determinare [l'offset datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Il formato per datetimeReference è [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Solo V3|false|Specifica se viene utilizzata l'entità esterna dell'utente [(con lo stesso nome dell'entità esistente)](#override-existing-model-predictions) o se l'entità esistente nel modello viene utilizzata per la stima. |
|`query`|string|Solo V3|Obbligatorio.|**In V2**, l'espressione da `q` stimare è nel parametro . <br><br>**In V3**, la funzionalità `query` viene passata nel parametro .|



## <a name="response-changes"></a>Modifiche alle risposte

La risposta alla query JSON è stata modificata per consentire un maggiore accesso a livello di codice ai dati utilizzati più di frequente.

### <a name="top-level-json-changes"></a>Modifiche JSON di livello superiore



Le proprietà JSON principali per `verbose` V2 sono, quando è impostato su true, che restituisce tutte le finalità e i relativi punteggi nella proprietà:The top JSON properties for V2 are, when is set to true, which returns all intents and their scores in the `intents` property:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Le principali proprietà JSON per V3 sono:The top JSON properties for V3 are:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

L'oggetto `intents` è un elenco non ordinato. Non presupporre che il `intents` primo `topIntent`figlio corrisponda a . Utilizzare invece `topIntent` il valore per trovare il punteggio:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Le modifiche dello schema JSON di risposta consentono:The response JSON schema changes allow for:

* Distinzione chiara tra `query`l'espressione originale, `prediction`, e la stima restituita, .
* Accesso a livello di codice più semplice ai dati previsti. Anziché enumerare una matrice in V2, è possibile accedere ai valori in base al **nome** sia per le finalità che per le entità. Per i ruoli di entità stimati, il nome del ruolo viene restituito perché è univoco nell'intera app.
* I tipi di dati, se determinati, vengono rispettati. I numeri numerici non vengono più restituiti come stringhe.
* Distinzione tra informazioni di stima della prima `$instance` priorità e metadati aggiuntivi, restituiti nell'oggetto.

### <a name="entity-response-changes"></a>Modifiche alle risposte alle entità

#### <a name="marking-placement-of-entities-in-utterances"></a>Contrassegno del posizionamento delle entità nelle espressioniMarking placement of entities in utterances

**In V2**un'entità è stata contrassegnata `endIndex`in un'espressione con e `startIndex` .

**In V3**, l'entità è contrassegnata con `startIndex` e `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>Accesso `$instance` per i metadati dell'entità

Se sono necessari metadati dell'entità, `verbose=true` la stringa di query `$instance` deve usare il flag e la risposta contiene i metadati nell'oggetto. Gli esempi sono illustrati nelle risposte JSON nelle sezioni seguenti.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Ogni entità stimata è rappresentata come matriceEach predicted entity is represented as an array

L'oggetto `prediction.entities.<entity-name>` contiene una matrice perché ogni entità può essere prevista più di una volta nell'espressione.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Modifiche alle entità predefinite

L'oggetto risposta V3 include modifiche alle entità predefinite. Per ulteriori informazioni, consulta [le entità predefinite specifiche.](luis-reference-prebuilt-entities.md)

#### <a name="list-entity-prediction-changes"></a>Elencare le modifiche di stima delle entitàList entity prediction changes

Il codice JSON per una stima dell'entità elenco è stato modificato in una matrice di matrici:The JSON for a list entity prediction has changed to to an array of arrays:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Ogni matrice interna corrisponde al testo all'interno dell'espressione. L'oggetto interior è una matrice perché lo stesso testo può essere visualizzato in più sottoelenco di un'entità elenco.

Quando si `entities` esegue il `$instance` mapping tra l'oggetto e l'oggetto, l'ordine degli oggetti viene mantenuto per le stime dell'entità elenco.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome del ruolo dell'entità anziché nome dell'entità

In V2, `entities` la matrice ha restituito tutte le entità stimate con il nome dell'entità come identificatore univoco. In V3, se l'entità usa ruoli e la stima è per un ruolo dell'entità, l'identificatore primario è il nome del ruolo. Ciò è possibile perché i nomi dei ruoli entità devono essere univoci nell'intera app, inclusi altri nomi di modelli (intent, entity).

Nell'esempio seguente: si consideri un'espressione che include il testo, `Yellow Bird Lane`. Questo testo viene previsto `Location` come ruolo `Destination`di un'entità personalizzata di .

|Testo dell'espressione|Nome dell'entità|Nome del ruolo|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

In V2, l'entità è identificata dal _nome dell'entità_ con il ruolo come proprietà dell'oggetto:

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

In V3, l'entità fa riferimento al _ruolo dell'entità,_ se la stima è per il ruolo:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

In V3, lo stesso `verbose` risultato con il flag per restituire i metadati dell'entità:In V3, the same result with the flag to return entity metadata:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Entità esterne passate al momento della stimaExternal entities passed in at prediction time

Le entità esterne consentono all'app LUIS di identificare ed etichettare le entità durante il runtime, che possono essere usate come funzionalità per le entità esistenti. In questo modo è possibile usare estrattori di entità personalizzati e personalizzati prima di inviare query all'endpoint di stima. Poiché questa operazione viene eseguita nell'endpoint di stima delle query, non è necessario eseguire nuovamente il training e pubblicare il modello.

L'applicazione client fornisce il proprio estrattore di entità gestendo la corrispondenza tra entità e determinando la posizione all'interno dell'espressione dell'entità corrispondente e quindi inviando tali informazioni con la richiesta.

Le entità esterne sono il meccanismo per estendere qualsiasi tipo di entità pur rimanendo utilizzato come segnali ad altri modelli, ad esempio ruoli, compositi e altri.

Ciò è utile per un'entità con dati disponibili solo in fase di esecuzione di stima delle query. Esempi di questo tipo di dati sono la modifica costante dei dati o di dati specifici per utente. È possibile estendere un'entità contatto LUIS con informazioni esterne dall'elenco contatti di un utente.

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

Nell'espressione precedente, l'espressione `him` viene utilizzata `Hazem`come riferimento a . Il chat bot conversazionale, nel `him` corpo POST, può eseguire il mapping `Hazem`al valore dell'entità estratto dalla prima espressione, .

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



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Elenchi dinamici passati al momento della stima

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

## <a name="deprecation"></a>Deprecazione

L'API V2 non sarà deprecata per almeno 9 mesi dopo l'anteprima V3.

## <a name="next-steps"></a>Passaggi successivi

Usare la documentazione dell'API V3 per aggiornare le chiamate REST esistenti alle API [dell'endpoint](https://aka.ms/luis-api-v3) LUIS.
