---
title: Elencare il tipo di entità-LUIS
description: Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzionalità consigli per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297238"
---
# <a name="list-entity"></a>Entità elenco

Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione **consigliata** per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente. Se sono presenti più entità elenco con lo stesso valore, ogni entità viene restituita nella query endpoint.

Un'entità elenco non è stata acquisita dal computer. Si tratta di una corrispondenza di testo esatta. LUIS contrassegna eventuali corrispondenze a un elemento in qualsiasi elenco come un'entità nella risposta.

**L'entità è una scelta ottimale quando i dati di testo:**

* Sono un set noto.
* Non cambia spesso. Se è necessario modificare spesso l'elenco o si desidera che l'elenco si espanda in modo automatico, è preferibile usare un'entità semplice con boosting con un elenco di frasi.
* Il set non supera i [limiti](luis-boundaries.md) massimi di LUIS per questo tipo di entità.
* Il testo nell'espressione è una corrispondenza senza distinzione tra maiuscole e minuscole con un sinonimo o il nome canonico. LUIS non usa l'elenco oltre la corrispondenza. La corrispondenza fuzzy, lo stemming, i plurali e altre varianti non vengono risolti con un'entità List. Per gestire le variazioni, è consigliabile usare un [criterio](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) con la sintassi del testo facoltativo.

![entità elenco](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Esempio. JSON da importare nell'entità elenco

  È possibile importare i valori in un'entità List esistente usando il formato JSON seguente:

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

|Elemento di elenco|Sinonimi elenco|
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


Si tratta del codice JSON se `verbose=false` è impostato nella stringa di query:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Si tratta del codice JSON se `verbose=true` è impostato nella stringa di query:

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

|Oggetto dati|Nome entità|Valore|
|--|--|--|
|Elenca entità|`Cities`|`paris`|


## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](tutorial-list-entity.md)si apprenderà come usare un' **entità list** per estrarre corrispondenze esatte del testo da un elenco di elementi noti.
