---
title: Modifiche all'endpoint di stima nell'API V3
titleSuffix: Azure Cognitive Services
description: Le API di query PREDICTION endpoint V3 sono state modificate. Usare questa guida per comprendere come eseguire la migrazione alle API endpoint della versione 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 7c2866441c7439008fad27ced9b9b1dddea848ec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492823"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Modifiche all'endpoint di stima per V3

Le API di query PREDICTION endpoint V3 sono state modificate. Usare questa guida per comprendere come eseguire la migrazione alle API endpoint della versione 3. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Stato disponibile** a livello generale: questa API v3 include modifiche significative di richiesta e risposta JSON dall'API v2.

L'API V3 fornisce le seguenti nuove funzionalità:

* [Entità esterne](#external-entities-passed-in-at-prediction-time)
* [Elenchi dinamici](#dynamic-lists-passed-in-at-prediction-time)
* [Modifiche JSON dell'entità predefinite](#prebuilt-entity-changes)

La [richiesta](#request-changes) e la [risposta](#response-changes) dell'endpoint di stima presentano modifiche significative per supportare le nuove funzionalità elencate in precedenza, incluse le seguenti:

* [Modifiche agli oggetti risposta](#top-level-json-changes)
* [Riferimenti nome ruolo entità anziché nome entità](#entity-role-name-instead-of-entity-name)
* [Proprietà per contrassegnare le entità negli enunciati](#marking-placement-of-entities-in-utterances)

La [documentazione di riferimento](https://aka.ms/luis-api-v3) è disponibile per V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 passa dalla versione di anteprima alla versione GA

V3 ha apportato le modifiche seguenti come parte del passaggio a GA: 

* Le seguenti entità predefinite hanno risposte JSON diverse: 
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Nome chiave unità misurabile da `units` a `unit`

* Modifica JSON del corpo della richiesta:
    * da `preferExternalEntities` a `preferExternalEntities`
    * parametro `score` facoltativo per entità esterne

* Modifiche JSON del corpo della risposta:
    * `normalizedQuery` rimosso

## <a name="suggested-adoption-strategy"></a>Strategia di adozione consigliata

Se si usa bot Framework, Controllo ortografico Bing V7 o si vuole eseguire la migrazione solo della creazione di app LUIS, continuare a usare l'endpoint V2. 

Se non si conosce alcuna applicazione client o integrazione (bot Framework e Controllo ortografico Bing V7), si è interessati alla migrazione della creazione di app LUIS e dell'endpoint di stima allo stesso tempo, iniziare a usare l'endpoint di stima V3. L'endpoint di stima v2 sarà ancora disponibile ed è una corretta strategia di fallback. 

## <a name="not-supported"></a>Supporto non disponibile

* L'API Controllo ortografico Bing non è supportata nell'endpoint di stima V3-continuare a usare l'endpoint di stima API v2 per le correzioni ortografiche

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework e applicazioni client del servizio Azure bot

Continuare a usare l'endpoint di stima dell'API v2 fino a quando non viene rilasciato V 4.7 di bot Framework. 

## <a name="v2-api-deprecation"></a>Deprecazione dell'API v2 

L'API di stima V2 non verrà deprecata per almeno 9 mesi dopo l'anteprima V3, 8RD giugno 2020. 

## <a name="endpoint-url-changes"></a>Modifiche dell'URL dell'endpoint 

### <a name="changes-by-slot-name-and-version-name"></a>Modifiche in base al nome dello slot e al nome della versione

Il formato della chiamata HTTP dell'endpoint V3 è stato modificato.

Per eseguire una query in base alla versione, è prima di tutto necessario [pubblicare tramite API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) con `"directVersionPublish":true`. Eseguire una query sull'endpoint che fa riferimento all'ID versione anziché al nome dello slot.

|VERSIONE DELL'API DI STIMA|Metodo|URL|
|--|--|--|
|V3|GET|https://<b>{Region}</b>. API.cognitive.Microsoft.com/Luis/<b>PREDICTION</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{nome-slot}</b>/Predict? query =<b>{query}</b>|
|V3|POST|https://<b>{Region}</b>. API.cognitive.Microsoft.com/Luis/<b>PREDICTION</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{nome-slot}</b>/Predict|
|V2|GET|https://<b>{Region}</b>. API.cognitive.Microsoft.com/Luis/<b>PREDICTION</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict? query =<b>{query}</b>|
|V2|POST|https://<b>{Region}</b>. API.cognitive.Microsoft.com/Luis/<b>PREDICTION</b><b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict|

|Valori validi per `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Richiedere modifiche 

### <a name="query-string-changes"></a>Modifiche della stringa di query

L'API V3 ha parametri di stringa di query diversi.

|Nome param|Type|Versione|Predefinito|Finalità|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Archivia query nel file di log. Il valore predefinito è False.| 
|`query`|string|Solo V3|Nessun valore predefinito: è obbligatorio nella richiesta GET|**Nella versione V2**, l'espressione da stimare si trova nel parametro `q`. <br><br>**Nella V3**la funzionalità viene passata nel parametro `query`.|
|`show-all-intents`|boolean|Solo V3|false|Restituisce tutti gli Intent con il punteggio corrispondente nell'oggetto **PREDICTION. Intent** . Gli Intent vengono restituiti come oggetti in un oggetto `intents` padre. Questo consente l'accesso a livello di codice senza la necessità di trovare l'intento in una matrice: `prediction.intents.give`. Nella versione V2 questi sono stati restituiti in una matrice. |
|`verbose`|boolean|V2 & V3|false|**Nella versione V2**, quando è impostato su true, vengono restituiti tutti gli Intent stimati. Se sono necessari tutti gli intenti previsti, usare il param V3 di `show-all-intents`.<br><br>**In V3**, questo parametro fornisce solo i dettagli relativi ai metadati dell'entità della stima delle entità.  |
|`timezoneOffset`|string|V2|-|Fuso orario applicato alle entità datetimeV2.|
|`datetimeReference`|string|V3|-|[Fuso orario](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) applicato alle entità datetimeV2. Sostituisce `timezoneOffset` dalla versione V2.|


### <a name="v3-post-body"></a>Corpo POST V3

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

|Proprietà|Type|Versione|Predefinito|Finalità|
|--|--|--|--|--|
|`dynamicLists`|array|Solo V3|Non obbligatorio.|Gli [elenchi dinamici](#dynamic-lists-passed-in-at-prediction-time) consentono di estendere un'entità di elenco con training e pubblicato esistente, già nell'app Luis.|
|`externalEntities`|array|Solo V3|Non obbligatorio.|Le [entità esterne](#external-entities-passed-in-at-prediction-time) offrono all'app Luis la possibilità di identificare ed etichettare entità durante il runtime, che possono essere usate come funzionalità per le entità esistenti. |
|`options.datetimeReference`|string|Solo V3|Nessun valore predefinito|Utilizzato per determinare l' [offset del datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Il formato di datetimeReference è [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Solo V3|false|Specifica se l' [entità esterna dell'utente (con lo stesso nome dell'entità esistente)](#override-existing-model-predictions) viene utilizzata o se per la stima viene utilizzata l'entità esistente del modello. |
|`query`|string|Solo V3|Richiesto.|**Nella versione V2**, l'espressione da stimare si trova nel parametro `q`. <br><br>**Nella V3**la funzionalità viene passata nel parametro `query`.|



## <a name="response-changes"></a>Modifiche della risposta

Il codice JSON per la risposta alla query è stato modificato per consentire un maggiore accesso programmatico ai dati usati più di frequente. 

### <a name="top-level-json-changes"></a>Modifiche JSON di primo livello



Le proprietà JSON principali per V2 sono, quando `verbose` è impostato su true, che restituisce tutti gli Intent e i relativi punteggi nella proprietà `intents`:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Le principali proprietà JSON per V3 sono:

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

L'oggetto `intents` è un elenco non ordinato. Non presupporre che il primo elemento figlio nella `intents` corrisponda al `topIntent`. Usare invece il valore `topIntent` per trovare il Punteggio:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Le modifiche dello schema JSON della risposta consentono:

* Distinzione netta tra espressione originale, `query`e stima restituita, `prediction`.
* Accesso programmatico più semplice ai dati stimati. Anziché eseguire l'enumerazione tramite una matrice nella versione V2, è possibile accedere ai valori in base al **nome** sia per gli Intent che per le entità. Per i ruoli di entità stimati, viene restituito il nome del ruolo perché è univoco nell'intera app.
* I tipi di dati, se determinati, vengono rispettati. I numeri non vengono più restituiti come stringhe.
* Distinzione tra le prime informazioni di stima di priorità e i metadati aggiuntivi restituiti nell'oggetto `$instance`. 

### <a name="entity-response-changes"></a>Modifiche della risposta dell'entità

#### <a name="marking-placement-of-entities-in-utterances"></a>Contrassegno del posizionamento delle entità negli enunciati

**Nella versione V2**, un'entità è stata contrassegnata in un enunciato con il `startIndex` e la `endIndex`. 

**In V3**l'entità è contrassegnata con `startIndex` e `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>`$instance` di accesso per i metadati dell'entità

Se sono necessari metadati di entità, la stringa di query deve usare il flag di `verbose=true` e la risposta contiene i metadati nell'oggetto `$instance`. Gli esempi vengono mostrati nelle risposte JSON nelle sezioni seguenti.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Ogni entità stimata è rappresentata come una matrice

L'oggetto `prediction.entities.<entity-name>` contiene una matrice perché ogni entità può essere stimata più di una volta nell'espressione. 

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Modifiche alle entità predefinite

L'oggetto risposta v3 include le modifiche apportate alle entità predefinite. Per altre informazioni, vedere [entità predefinite specifiche](luis-reference-prebuilt-entities.md) . 

#### <a name="list-entity-prediction-changes"></a>Elencare le modifiche di stima delle entità

Il codice JSON per la stima di un'entità di elenco è stato modificato in una matrice di matrici:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Ogni matrice interna corrisponde al testo all'interno dell'espressione. L'oggetto interno è una matrice perché lo stesso testo può essere visualizzato in più di un sottoelenco di un'entità elenco. 

Quando si esegue il mapping tra l'oggetto `entities` e l'oggetto `$instance`, l'ordine degli oggetti viene mantenuto per le stime di entità dell'elenco.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome ruolo entità anziché nome entità 

Nella versione V2 la matrice di `entities` ha restituito tutte le entità stimate con il nome dell'entità che è l'identificatore univoco. In V3, se l'entità utilizza ruoli e la stima è per un ruolo entità, l'identificatore primario è il nome del ruolo. Questo è possibile perché i nomi dei ruoli di entità devono essere univoci nell'intera app, inclusi altri nomi di modello (finalità, entità).

Nell'esempio seguente viene considerato un enunciato che include il testo `Yellow Bird Lane`. Questo testo viene stimato come il ruolo dell'entità `Location` personalizzata di `Destination`.

|Testo enunciato|Nome dell'entità|Nome del ruolo|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Nella versione V2 l'entità viene identificata dal _nome dell'entità_ con il ruolo come proprietà dell'oggetto:

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

In V3, all'entità viene fatto riferimento dal _ruolo entità_, se la stima è per il ruolo:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

In V3, lo stesso risultato con il flag di `verbose` per restituire i metadati dell'entità:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Entità esterne passate in fase di stima

Le entità esterne offrono all'app LUIS la possibilità di identificare ed etichettare entità durante il runtime, che possono essere usate come funzionalità per le entità esistenti. In questo modo è possibile utilizzare estrattiri di entità distinti e personalizzati prima di inviare query all'endpoint di stima. Poiché questa operazione viene eseguita nell'endpoint di stima della query, non è necessario ripetere il training e pubblicare il modello.

L'applicazione client fornisce il proprio estrattore di entità gestendo la corrispondenza delle entità e determinando la posizione nell'espressione dell'entità corrispondente e quindi inviando tali informazioni con la richiesta. 

Le entità esterne sono il meccanismo per l'estensione di qualsiasi tipo di entità, mentre continuano a essere usati come segnali per altri modelli, ad esempio ruoli, composito e altri.

Questa operazione è utile per un'entità che dispone di dati disponibili solo nel runtime di stima query. Esempi di questo tipo di dati sono dati in costante evoluzione o specifici per utente. È possibile estendere un'entità Contact LUIS con informazioni esterne dall'elenco dei contatti di un utente. 

### <a name="entity-already-exists-in-app"></a>L'entità esiste già nell'app

Il valore di `entityName` per l'entità esterna, passato nel corpo della richiesta dell'endpoint, deve esistere già nell'app sottoposta a training e pubblicata al momento della richiesta. Il tipo di entità non è rilevante, sono supportati tutti i tipi.

### <a name="first-turn-in-conversation"></a>Primo turno di conversazione

Si consideri un primo enunciato in una conversazione bot chat in cui un utente immette le informazioni incomplete seguenti:

`Send Hazem a new message`

La richiesta dal bot della chat a LUIS può passare informazioni nel corpo del POST sulle `Hazem` in modo che corrisponda direttamente a uno dei contatti dell'utente.

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

Nell'espressione precedente, l'espressione USA `him` come riferimento a `Hazem`. Il bot della chat conversazione, nel corpo POST, può eseguire il mapping `him` al valore dell'entità Estratto dal primo enunciato, `Hazem`.

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

La proprietà Opzioni `preferExternalEntities` specifica che se l'utente invia un'entità esterna che si sovrappone a un'entità stimata con lo stesso nome, LUIS sceglie l'entità passata o l'entità esistente nel modello. 

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

Se la `preferExternalEntities` è impostata su `false`, LUIS restituisce una risposta come se non fosse stata inviata l'entità esterna. 

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

Se la `preferExternalEntities` è impostata su `true`, LUIS restituisce una risposta che include:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Risoluzione

La proprietà _facoltativa_ `resolution` restituisce nella risposta di stima, consentendo di passare i metadati associati all'entità esterna, quindi di riceverli di nuovo nella risposta. 

Lo scopo principale consiste nell'estendere le entità predefinite, ma questo non è limitato a tale tipo di entità. 

Il `resolution` proprietà può essere un numero, una stringa, un oggetto o una matrice:

* Dallas
* {"Text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Elenchi dinamici passati in fase di stima

Gli elenchi dinamici consentono di estendere un'entità di elenco con training e pubblicato esistente, già nell'app LUIS. 

Usare questa funzionalità quando i valori dell'entità elenco devono essere modificati periodicamente. Questa funzionalità consente di estendere un'entità di elenco già sottoposta a training e pubblicato:

* Al momento della richiesta dell'endpoint di stima della query.
* Per una singola richiesta.

L'entità List può essere vuota nell'app LUIS, ma deve esistere. L'entità List nell'app LUIS non è stata modificata, ma la capacità di stima all'endpoint viene estesa per includere fino a 2 elenchi con circa 1.000 elementi.

### <a name="dynamic-list-json-request-body"></a>Corpo della richiesta JSON dell'elenco dinamico

Inviare il corpo JSON seguente per aggiungere un nuovo sottoelenco con sinonimi all'elenco e stimare l'entità list per il testo, `LUIS`con la richiesta di stima di `POST` query:

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

## <a name="deprecation"></a>Deprecazione 

L'API v2 non verrà deprecata per almeno 9 mesi dopo l'anteprima V3. 

## <a name="next-steps"></a>Passaggi successivi

Usare la documentazione dell'API V3 per aggiornare le chiamate REST esistenti alle API dell' [endpoint](https://aka.ms/luis-api-v3) Luis. 
