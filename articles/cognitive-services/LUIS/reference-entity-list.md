---
title: Tipo di entità elenco - LUISList entity type - LUIS
description: Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione consigliata per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297238"
---
# <a name="list-entity"></a>Entità elenco

Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione **consigliata** per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente. Se sono presenti più entità elenco con lo stesso valore, ogni entità viene restituita nella query endpoint.

Un'entità list non è appresa in modo automatico. Si tratta di una corrispondenza di testo esatta. LUIS contrassegna eventuali corrispondenze a un elemento in qualsiasi elenco come un'entità nella risposta.

**Questa entità è idonea quando i dati di testo:**

* Sono un set noto.
* Non cambia spesso. Se è necessario modificare spesso l'elenco o si desidera che l'elenco si espanda autonomamente, una semplice entità potenziata con un elenco di frasi è una scelta migliore.
* Il set non supera i [limiti](luis-boundaries.md) massimi di LUIS per questo tipo di entità.
* Il testo nell'espressione è una corrispondenza senza distinzione tra maiuscole e minuscole con un sinonimo o il nome canonico. LUIS non utilizza l'elenco oltre la partita. La corrispondenza fuzzy, lo stemming, il plurale e altre varianti non vengono risolti con un'entità elenco. Per gestire le variazioni, è consigliabile usare un [criterio](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) con la sintassi del testo facoltativo.

![entità elenco](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Esempio .json da importare nell'entità elencoExample .json to import into list entity

  È possibile importare valori in un'entità elenco esistente utilizzando il formato json seguente:You can import values into an existing list entity using the following .json format:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Risposta JSON di esempio

Si supponga che l'app disponga di un elenco, di nome `Cities`, consentendo variazioni di nomi di città, tra cui città dell'aeroporto (Sea-tac), codice dell'aeroporto (SEA), codice postale (98101) e prefisso telefonico (206).

|Elemento elenco|Sinonimi elenco|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Nell'espressione precedente la parola `paris` viene mappata all'elemento parigi come parte dell'entità elenco `Cities`. L'entità elenco corrisponde al nome normalizzato dell'elemento e ai sinonimi dell'elemento.

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)


Questo è il `verbose=false` codice JSON se è impostato nella stringa di query:This is the JSON if is set in the query string:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Questo è il `verbose=true` codice JSON se è impostato nella stringa di query:This is the JSON if is set in the query string:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Oggetto dati|Nome dell'entità|valore|
|--|--|--|
|Entità elenco|`Cities`|`paris`|


## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](tutorial-list-entity.md)viene illustrato come utilizzare **un'entità elenco** per estrarre corrispondenze esatte di testo da un elenco di elementi noti.
