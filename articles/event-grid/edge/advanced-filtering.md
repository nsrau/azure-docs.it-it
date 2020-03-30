---
title: Filtro avanzato - Azure Event Grid IoT Edge Documenti Microsoft
description: Filtro avanzato in Griglia di eventi su Edge IoT.Advanced filtering in Event Grid on IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992561"
---
# <a name="advanced-filtering"></a>Filtro avanzato
Griglia di eventi consente di specificare i filtri su qualsiasi proprietà nel payload json. Questi filtri sono modellati `AND` come set di condizioni, con ogni condizione esterna con condizioni interne `OR` facoltative. Per `AND` ogni condizione, specificare i seguenti valori:

* `OperatorType`- Il tipo di confronto.
* `Key`- Percorso json della proprietà a cui applicare il filtro.
* `Value`- Il valore di riferimento su cui `Values` viene eseguito il filtro (o) - Il set di valori di riferimento su cui viene eseguito il filtro.

## <a name="json-syntax"></a>Sintassi JSON

La sintassi JSON per un filtro avanzato è la seguente:The JSON syntax for an advanced filter is as follows:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Applicazione di filtri ai valori di matrice

Griglia di eventi non supporta oggi il filtro su una matrice di valori. Se un evento in ingresso ha un valore di matrice per la chiave del filtro avanzato, l'operazione di corrispondenza ha esito negativo. L'evento in ingresso non corrisponde alla sottoscrizione dell'evento.

## <a name="and-or-not-semantics"></a>Semantica AND-OR-NOT

Si noti che nell'esempio json fornito in precedenza, `AdvancedFilters` è una matrice. Considerare ogni `AdvancedFilter` elemento della `AND` matrice come una condizione.

Per gli operatori che supportano `NumberIn` `NumberNotIn`più `StringIn`valori (ad esempio , `OR` , , e così via), ogni valore viene considerato come una condizione. Pertanto, `StringBeginsWith("a", "b", "c")` a corrisponderà a qualsiasi `a` `b` valore `c`stringa che inizia con o o .

> [!CAUTION]
> Gli operatori `NumberNotIn` NOT `StringNotIn` - e si comportano come `Values` condizioni AND su ogni valore specificato nel campo.
>
> In caso contrario, il filtro sarà un filtro Accept-All e verrà vanificato lo scopo del filtro.

## <a name="floating-point-rounding-behavior"></a>Comportamento di arrotondamento a virgola mobile

Griglia di `decimal` eventi utilizza il tipo .NET per gestire tutti i valori numerici. I valori numerici specificati nella sottoscrizione di eventi JSON non sono soggetti a un comportamento di arrotondamento a virgola mobile.

## <a name="case-sensitivity-of-string-filters"></a>Distinzione tra maiuscole e minuscole nei filtri stringa

Tutti i confronti tra stringhe non fanno distinzione tra maiuscole e minuscole. Non c'è modo di cambiare questo comportamento oggi.

## <a name="allowed-advanced-filter-keys"></a>Tasti di filtro avanzati consentiti

La `Key` proprietà può essere una proprietà di primo livello nota o essere un percorso json con più punti, in cui ogni punto indica l'esecuzione di un oggetto json annidato.

Griglia di eventi non ha alcun `$` significato speciale per il carattere nella chiave, a differenza della specifica JSONPath.

### <a name="event-grid-schema"></a>Schema della griglia di eventi

Per gli eventi nello schema Griglia di eventi:

* ID
* Argomento
* Oggetto
* EventType
* DataVersion
* Data.Prop1
* Data.Prop-Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Schema di eventi personalizzato

Non esiste alcuna `Key` restrizione sullo schema di eventi in personalizzato poiché Griglia di eventi non impone alcuno schema di busta sul payload.

## <a name="numeric-single-value-filter-examples"></a>Esempi numerici di filtri a valore singolo

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Esempi di filtri numerici valore intervallo-valore

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Esempi di filtri valore intervallo stringa

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Esempi di filtri booleani a valore singolo

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
