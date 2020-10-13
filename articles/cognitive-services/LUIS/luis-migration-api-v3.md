---
title: Modifiche all'endpoint di stima nell'API V3
description: Le API di query PREDICTION endpoint V3 sono state modificate. Usare questa guida per comprendere come eseguire la migrazione alle API endpoint della versione 3.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: a5760db2d6e453d631680d6154e6d9a03ce55cd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541340"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Modifiche all'endpoint di stima per V3

Le API di query PREDICTION endpoint V3 sono state modificate. Usare questa guida per comprendere come eseguire la migrazione alle API endpoint della versione 3.

**Stato disponibile** a livello generale: questa API v3 include modifiche significative di richiesta e risposta JSON dall'API v2.

L'API V3 fornisce le seguenti nuove funzionalità:

* [Entità esterne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Elenchi dinamici](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
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
    * `score`parametro facoltativo per entità esterne

* Modifiche JSON del corpo della risposta:
    * `normalizedQuery` rimosso

## <a name="suggested-adoption-strategy"></a>Strategia di adozione consigliata

Se si usa bot Framework, Controllo ortografico Bing V7 o si vuole eseguire la migrazione solo della creazione di app LUIS, continuare a usare l'endpoint V2.

Se non si conosce alcuna applicazione client o integrazione (bot Framework e Controllo ortografico Bing V7), si è interessati alla migrazione della creazione di app LUIS e dell'endpoint di stima allo stesso tempo, iniziare a usare l'endpoint di stima V3. L'endpoint di stima v2 sarà ancora disponibile ed è una corretta strategia di fallback.


## <a name="not-supported"></a>Non supportato

### <a name="bing-spell-check"></a>Controllo ortografico Bing

Questa API non è supportata nell'endpoint di stima V3-continuare a usare l'endpoint di stima dell'API v2 per le correzioni ortografiche. Se è necessaria la correzione ortografica quando si usa l'API V3, fare in modo che l'applicazione client chiami l'API [controllo ortografico Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) e modifichi il testo con l'ortografia corretta, prima di inviare il testo all'API Luis.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework e applicazioni client del servizio Azure bot

Continuare a usare l'endpoint di stima dell'API v2 fino a quando non viene rilasciato V 4.7 di bot Framework.

## <a name="v2-api-deprecation"></a>Deprecazione dell'API v2

L'API di stima V2 non verrà deprecata per almeno 9 mesi dopo l'anteprima V3, 8 giugno 2020.

## <a name="endpoint-url-changes"></a>Modifiche dell'URL dell'endpoint

### <a name="changes-by-slot-name-and-version-name"></a>Modifiche in base al nome dello slot e al nome della versione

Il [formato della chiamata http dell'endpoint V3](developer-reference-resource.md#rest-endpoints) è stato modificato.

Per eseguire una query in base alla versione, è prima di tutto necessario [pubblicare tramite l'API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) con `"directVersionPublish":true` . Eseguire una query sull'endpoint che fa riferimento all'ID versione anziché al nome dello slot.

|Valori validi per `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Richiedere modifiche

### <a name="query-string-changes"></a>Modifiche della stringa di query

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

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

|Proprietà|Type|Versione|Predefinito|Scopo|
|--|--|--|--|--|
|`dynamicLists`|array|Solo V3|Non obbligatorio.|Gli [elenchi dinamici](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) consentono di estendere un'entità di elenco con training e pubblicato esistente, già nell'app Luis.|
|`externalEntities`|array|Solo V3|Non obbligatorio.|Le [entità esterne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) offrono all'app Luis la possibilità di identificare ed etichettare entità durante il runtime, che possono essere usate come funzionalità per le entità esistenti. |
|`options.datetimeReference`|string|Solo V3|Nessun valore predefinito|Utilizzato per determinare l' [offset del datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Il formato di datetimeReference è [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Solo V3|false|Specifica se l' [entità esterna dell'utente (con lo stesso nome dell'entità esistente)](schema-change-prediction-runtime.md#override-existing-model-predictions) viene utilizzata o se per la stima viene utilizzata l'entità esistente del modello. |
|`query`|string|Solo V3|Obbligatorio.|**Nella versione V2**, l'espressione da stimare si trova nel `q` parametro. <br><br>**Nella V3**la funzionalità viene passata nel `query` parametro.|

## <a name="response-changes"></a>Modifiche della risposta

Il codice JSON per la risposta alla query è stato modificato per consentire un maggiore accesso programmatico ai dati usati più di frequente.

### <a name="top-level-json-changes"></a>Modifiche JSON di primo livello



Le prime proprietà JSON per V2 sono, quando `verbose` è impostato su true, che restituisce tutti gli Intent e i relativi punteggi nella `intents` proprietà:

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

L' `intents` oggetto è un elenco non ordinato. Non presupporre che il primo elemento figlio nell'oggetto `intents` corrisponda a `topIntent` . Usare invece il `topIntent` valore per trovare il Punteggio:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Le modifiche dello schema JSON della risposta consentono:

* Distinzione netta tra espressione originale, `query` e stima restituita, `prediction` .
* Accesso programmatico più semplice ai dati stimati. Anziché eseguire l'enumerazione tramite una matrice nella versione V2, è possibile accedere ai valori in base al **nome** sia per gli Intent che per le entità. Per i ruoli di entità stimati, viene restituito il nome del ruolo perché è univoco nell'intera app.
* I tipi di dati, se determinati, vengono rispettati. I numeri non vengono più restituiti come stringhe.
* Distinzione tra le prime informazioni di stima di priorità e i metadati aggiuntivi restituiti nell' `$instance` oggetto.

### <a name="entity-response-changes"></a>Modifiche della risposta dell'entità

#### <a name="marking-placement-of-entities-in-utterances"></a>Contrassegno del posizionamento delle entità negli enunciati

**Nella versione V2**, un'entità è stata contrassegnata in un enunciato con `startIndex` e `endIndex` .

**In V3**l'entità è contrassegnata con `startIndex` e `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Accesso `$instance` per i metadati dell'entità

Se sono necessari metadati di entità, la stringa di query deve usare il `verbose=true` flag e la risposta contiene i metadati nell' `$instance` oggetto. Gli esempi vengono mostrati nelle risposte JSON nelle sezioni seguenti.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Ogni entità stimata è rappresentata come una matrice

L' `prediction.entities.<entity-name>` oggetto contiene una matrice perché ogni entità può essere stimata più di una volta nell'espressione.

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

Quando si esegue il mapping tra l' `entities` oggetto `$instance` e l'oggetto, l'ordine degli oggetti viene mantenuto per le stime di entità dell'elenco.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome ruolo entità anziché nome entità

Nella versione V2 la `entities` matrice ha restituito tutte le entità stimate con il nome dell'entità che rappresenta l'identificatore univoco. In V3, se l'entità utilizza ruoli e la stima è per un ruolo entità, l'identificatore primario è il nome del ruolo. Questo è possibile perché i nomi dei ruoli di entità devono essere univoci nell'intera app, inclusi altri nomi di modello (finalità, entità).

Nell'esempio seguente: si consideri un enunciato che include il testo `Yellow Bird Lane` . Questo testo viene stimato come il `Location` ruolo di un'entità personalizzata di `Destination` .

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

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Estendere l'app in fase di stima

Informazioni sui [concetti](schema-change-prediction-runtime.md) relativi all'estensione dell'app al runtime di previsione.

## <a name="deprecation"></a>Deprecazione

L'API v2 non verrà deprecata per almeno 9 mesi dopo l'anteprima V3.

## <a name="next-steps"></a>Passaggi successivi

Usare la documentazione dell'API V3 per aggiornare le chiamate REST esistenti alle API dell' [endpoint](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) Luis.
