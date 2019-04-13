---
title: Estrazione dei dati
titleSuffix: Language Understanding - Azure Cognitive Services
description: Estrarre dati da un testo utterance con finalità ed entità. Informazioni su quale tipo di dati può essere estratti da LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 35f1521884de3a4a0971b6e1c00f92a9094a8550
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526290"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Estrarre dati da un testo utterance con finalità ed entità
LUIS consente di ottenere informazioni da espressioni in linguaggio naturale dell'utente. Le informazioni vengono estratte in modo che possano essere usate da un programma, applicazione o chatbot per intervenire. Le sezioni seguenti spiegano quali dati vengono restituiti da finalità ed entità con esempi di JSON.

I dati più difficili da estrarre sono i dati appresi in modo automatico perché non rappresentano una corrispondenza di testo esatta. L'estrazione dati delle [entità](luis-concept-entity-types.md) apprese in modo automatico deve far parte del [ciclo di creazione](luis-concept-app-iteration.md) finché non si è certi di ricevere i dati previsti.

## <a name="data-location-and-key-usage"></a>Posizione dei dati e uso di chiavi
LUIS fornisce i dati dall'[endpoint](luis-glossary.md#endpoint) pubblicato. La **richiesta HTTPS** (POST o GET) contiene l'espressione e alcune configurazioni facoltative, ad esempio ambienti di staging o produzione.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID` è disponibile nella pagina **Settings** (Impostazioni) dell'app LUIS e in parte dell'URL (dopo `/apps/`) quando si modifica quell'app LUIS. `subscription-key` è la chiave endpoint usata per eseguire query nell'app. Sebbene sia possibile usare la chiave di creazione/avvio durante l'apprendimento di LUIS, è importante modificare la chiave endpoint in una chiave che supporti l'[uso previsto di LUIS](luis-boundaries.md#key-limits). L'unità `timezoneOffset` è in minuti.

La **risposta HTTPS** contiene tutte le informazioni su finalità ed entità che LUIS è in grado di determinare in base al modello attualmente pubblicato dell'endpoint di staging o produzione. L'URL dell'endpoint è disponibile nel sito Web [LUIS](luis-reference-regions.md), nella sezione **Manage** (Gestisci) della pagina **Keys and endpoints** (Chiavi ed endpoint).

## <a name="data-from-intents"></a>Dati da finalità
I dati principali sono dati dal **nome della finalità** con il punteggio superiore. Usando l'`MyStore`[avvio rapido](luis-quickstart-intents-only.md), la risposta dell'endpoint è:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Oggetto dati|Tipo di dati|Posizione dei dati|Valore|
|--|--|--|--|
|Finalità|string|topScoringIntent.intent|"GetStoreInfo"|

Se il chatbot o l'app chiamante LUIS prende una decisione in base a più di un punteggio di finalità, restituire i punteggi delle finalità impostando il parametro querystring, `verbose=true`. La risposta dell'endpoint è:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

Le finalità vengono ordinate dal punteggio più alto al più basso.

|Oggetto dati|Tipo di dati|Posizione dei dati|Valore|Score|
|--|--|--|--|:--|
|Finalità|string|intents[0].intent|"GetStoreInfo"|0,984749258|
|Finalità|string|intents[0].intent|"None"|0,0168218873|

Se si aggiungono domini predefiniti, il nome della finalità indica il dominio, ad esempio `Utilties` o `Communication` e la finalità:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

|Domain|Oggetto dati|Tipo di dati|Posizione dei dati|Valore|
|--|--|--|--|--|
|Servizi pubblici|Finalità|string|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Comunicazione|Finalità|string|intents[0].intent|<b>Communication</b>.StartOver"|
||Finalità|string|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Dati da entità
La maggior parte dei chatbot e delle applicazioni richiede più del nome della finalità. Questi dati aggiuntivi e facoltativi provengono da entità trovate nell'espressione. Ogni tipo di entità restituisce informazioni diverse sulla corrispondenza.

Una singola parola o frase dell'espressione può corrispondere a più di un'entità. In questo caso, ogni entità corrispondente viene restituita con il relativo punteggio.

Tutte le entità vengono restituite nella matrice **entità** della risposta dall'endpoint:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>Entità in formato token restituita
Diverse [impostazioni cultura](luis-language-support.md#tokenization) restituiscono l'oggetto entità con il `entity` valore [in formato token](luis-glossary.md#token). Gli elementi startIndex e endIndex restituiti da LUIS nell'oggetto entità non corrispondono al nuovo valore in formato token, ma alla query originale affinché sia possibile estrarre l'entità non elaborata in modo programmatico. 

Ad esempio, in tedesco, la parola `das Bauernbrot` in formato token è `das bauern brot`. Il valore in formato token, `das bauern brot`, viene restituito e il valore originale può essere determinato in modo programmatico dagli elementi startIndex ed endIndex della query originale, restituendo `das Bauernbrot`.

## <a name="simple-entity-data"></a>Dati entità semplice

Un'[entità semplice](luis-concept-entity-types.md) è un valore appreso in modo automatico. Può trattarsi di una parola o di una frase.

`Bob Jones wants 3 meatball pho`

Nell'espressione precedente `Bob Jones` è etichettata come entità `Customer` semplice.

I dati restituiti dall'endpoint includono il nome dell'entità, il testo individuato nell'espressione, la posizione del testo individuato e il punteggio:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Oggetto dati|Nome dell'entità|Valore|
|--|--|--|
|Entità semplice|`Customer`|`bob jones`|

## <a name="hierarchical-entity-data"></a>Dati entità gerarchica

**Entità gerarchiche sarà deprecata. Uso [ruoli entità](luis-concept-roles.md) determinare sottotipi dell'entità, invece di entità gerarchiche.**

Le entità [gerarchiche](luis-concept-entity-types.md) sono apprese in modo automatico e possono includere una parola o una frase. Gli elementi figlio vengono identificati in base al contesto. Se si cerca una relazione padre-figlio con corrispondenza di testo esatta, usare un'entità [List](#list-entity-data) (elenco).

`book 2 tickets to paris`

Nell'espressione precedente `paris` viene etichettata come elemento figlio `Location::ToLocation` dell'entità gerarchica `Location`.

I dati restituiti dall'endpoint includono il nome dell'entità e il nome dell'elemento figlio, il testo individuato nell'espressione, la posizione del testo individuato e il punteggio:

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Oggetto dati|Padre|Figlio|Valore|
|--|--|--|--|
|Entità gerarchica|Località|ToLocation|"paris"|

## <a name="composite-entity-data"></a>Dati entità composita
Le entità [composite](luis-concept-entity-types.md) vengono apprese in modo automatico e possono includere una parola o una frase. Ad esempio, considerare un'entità composita di `number` e `Location::ToLocation` predefiniti con l'espressione seguente:

`book 2 tickets to paris`

Si noti che tra `2`, il numero, `paris` e ToLocation sono presenti delle parole che non fanno parte di nessuna delle entità. La sottolineatura verde, usata in un'espressione etichettata nel sito Web [LUIS](luis-reference-regions.md), indica un'entità composita.

![Entità composita](./media/luis-concept-data-extraction/composite-entity.png)

Le entità composite vengono restituite in una matrice `compositeEntities` e tutte le entità nell'entità composita vengono restituite nella matrice `entities`:

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Oggetto dati|Nome dell'entità|Valore|
|--|--|--|
|Entità predefinita - numero|"builtin.number"|"2"|
|Entità gerarchica - posizione|"Location::ToLocation"|"paris"|

## <a name="list-entity-data"></a>Dati entità elenco

Un'entità [list](luis-concept-entity-types.md) non è appresa in modo automatico. Si tratta di una corrispondenza di testo esatta. Un elenco rappresenta gli elementi dell'elenco insieme ai sinonimi di tali elementi. LUIS contrassegna eventuali corrispondenze a un elemento in qualsiasi elenco come un'entità nella risposta. Un sinonimo può trovarsi in più di un elenco.

Si supponga che l'app disponga di un elenco, di nome `Cities`, consentendo variazioni di nomi di città, tra cui città dell'aeroporto (Sea-tac), codice dell'aeroporto (SEA), codice postale (98101) e prefisso telefonico (206).

|Elemento elenco|Sinonimi elenco|
|---|---|
|Seattle|sea-tac, sea, 98101, 206, +1 |
|Parigi|cdg, roissy, ory, 75001, 1, +33|

`book 2 tickets to paris`

Nell'espressione precedente la parola `paris` viene mappata all'elemento parigi come parte dell'entità elenco `Cities`. L'entità elenco corrisponde al nome normalizzato dell'elemento e ai sinonimi dell'elemento.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Un'altra espressione di esempio, usando un sinonimo per Parigi:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>Dati entità predefinita
Vengono trovate le entità [predefinite](luis-concept-entity-types.md) in base alla corrispondenza con espressione regolare usando il progetto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) open-source. Le entità predefinite vengono restituite nella matrice di entità e usano il nome tipo con prefisso `builtin::`. Il testo seguente è un'espressione di esempio con entità predefinite restituite:

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

## <a name="regular-expression-entity-data"></a>Dati entità espressione regolare
Le entità [espressione regolare](luis-concept-entity-types.md) vengono individuate in base a una corrispondenza con espressione regolare usando un'espressione fornita alla creazione dell'entità. Quando si usa `kb[0-9]{6}` come definizione di entità espressione regolare, la risposta JSON seguente è un'espressione di esempio con le entità espressione regolare restituite per la query `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Estrazione di nomi
Ottenere i nomi da un'espressione è difficile perché un nome può essere qualsiasi combinazione di lettere e parole. Le opzioni disponibili variano a seconda del tipo di nome estratto. I suggerimenti seguenti non sono regole, ma linee guida.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Aggiungere le entità PersonName e GeographyV2 predefinite

Le entità [PersonName](luis-reference-prebuilt-person.md) e [GeographyV2](luis-reference-prebuilt-geographyV2.md) sono disponibili in alcune [impostazioni cultura della lingua](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Nomi di persone

I nomi delle persone possono presentare un formato a seconda della lingua e delle impostazioni cultura. Usare un precompilati **[personName](luis-reference-prebuilt-person.md)** entità o un **[entità semplice](luis-concept-entity-types.md#simple-entity)** con [ruoli](luis-concept-roles.md) del primo e Cognome. 

Se si usa l'entità semplice, assicurarsi di fornire esempi che usano il nome e cognome in diverse parti del utterance, nelle espressioni di lunghezze diverse e utterances tra tutti gli Intent tra cui nessun intent. [Rivedere](luis-how-to-review-endoint-utt.md) regolarmente le espressioni endpoint per etichettare qualsiasi nome non stimato correttamente.

### <a name="names-of-places"></a>Nomi di località

I nomi delle località vengono impostati come città, province, stati e nazioni e sono noti come tali. Utilizzare l'entità precompilato **[geographyV2](luis-reference-prebuilt-geographyv2.md)** per estrarre le informazioni sulla posizione.

### <a name="new-and-emerging-names"></a>Nomi nuovi ed emergenti

Alcune app devono essere in grado di trovare nomi nuovi ed emergenti, ad esempio prodotti o aziende. Questi tipi di nomi sono il tipo più difficile di estrazione dei dati. Iniziare con una **[entità semplice](luis-concept-entity-types.md#simple-entity)** e aggiungere un [elenco frasi](luis-concept-feature.md). [Rivedere](luis-how-to-review-endoint-utt.md) regolarmente le espressioni endpoint per etichettare qualsiasi nome non stimato correttamente.

## <a name="pattern-roles-data"></a>Dati ruoli criterio
I ruoli sono differenze contestuali di entità.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Dati entità Pattern.any
Le entità pattern.Any sono entità a lunghezza variabile usate nelle espressioni modello di un [criterio](luis-concept-patterns.md).

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>Analisi del sentiment
Se l'analisi del sentiment è configurata, è inclusa nella risposta json LUIS. Per ulteriori informazioni sull'analisi del sentiment, vedere la documentazione [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

### <a name="sentiment-data"></a>Dati sentiment
I dati sentiment sono un punteggio compreso tra 1 e 0 che indica il sentiment positivo (più vicino a 1) o negativo (più vicino a 0) dei dati.

Quando le impostazioni cultura sono `en-us`, la risposta è:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Per tutte le altre impostazioni cultura, la risposta è:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Dati entità estrazione frasi chiave
L'entità estrazione frasi chiave restituisce frasi chiave nell'espressione, fornita da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>Dati corrispondenti a più entità

LUIS restituisce tutte le entità individuate nell'espressione. Di conseguenza, il chatbot potrebbe prendere decisioni in base ai risultati. In un'espressione possono essere presenti più entità:

`book me 2 adult business tickets to paris tomorrow on air france`

L'endpoint LUIS può individuare gli stessi dati in entità diverse:

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Dati corrispondenti a più entità elenco

Se una parola o frase corrisponde a più entità elenco, la query di endpoint restituisce ciascuna entità elenco.

Per la query `when is the best time to go to red rock?`, se l'app contiene la parola `red` in più di un elenco, LUIS riconosce tutte le entità e restituisce una matrice di entità come parte della risposta dell'endpoint JSON: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.
