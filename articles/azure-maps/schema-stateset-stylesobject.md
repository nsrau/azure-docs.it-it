---
title: Guida di riferimento allo schema StylesObject per le mappe di Azure dinamiche
description: Guida di riferimento alla sintassi e allo schema StylesObject di Azure Maps dinamici.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/20/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f6bc4c62febf24dee790ac6136b1661426d4d619
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536949"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Guida di riferimento allo schema StylesObject per mappe dinamiche

 `StylesObject`È una `StyleObject` matrice che rappresenta gli stili degli Stati. Usare il [servizio stato funzionalità](/rest/api/maps/featurestate) di Azure Maps Creator per applicare gli stili di stato alle funzionalità dei dati della mappa interna. Una volta creati gli stili dello stato e associati alle funzionalità della mappa interna, è possibile usarli per creare mappe interne dinamiche. Per altre informazioni sulla creazione di mappe interne dinamiche, vedere [implementare lo stile dinamico per le mappe interne dell'autore](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

Una `StyleObject` è una delle seguenti regole di stile:

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

Il codice JSON seguente mostra un esempio di utilizzo di ognuno dei tre tipi di stile.  `BooleanTypeStyleRule`Viene utilizzato per determinare lo stile dinamico per le funzionalità la cui `occupied` proprietà è true e false.  `NumericTypeStyleRule`Viene utilizzato per determinare lo stile per le funzionalità la cui `temperature` Proprietà rientra in un determinato intervallo. Infine, `StringTypeStyleRule` viene usato per trovare la corrispondenza con stili specifici a `meetingType` .



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 Un oggetto `NumericTypeStyleRule` è un oggetto  [`StyleObject`](#styleobject) ed è costituito dalle proprietà seguenti:

| Proprietà | Type | Descrizione | Obbligatoria |
|-----------|----------|-------------|-------------|
| `keyName` | string | Nome della proprietà dinamica o *dello stato* . Un oggetto `keyName` deve essere univoco all'interno della `StyleObject` matrice.| Sì |
| `type` | string | Il valore è "NUMERIC". | Sì |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Matrice di intervalli di stili numerici con i colori associati. Ogni intervallo definisce un colore da usare quando il valore *dello stato* soddisfa l'intervallo.| Sì |

### <a name="numberruleobject"></a>NumberRuleObject

Un oggetto `NumberRuleObject` è costituito da un oggetto [`RangeObject`](#rangeobject) e da una `color` Proprietà. Se il valore *dello stato* rientra nell'intervallo, il colore per la visualizzazione sarà il colore specificato nella `color` Proprietà.

Se si definiscono più intervalli sovrapposti, il colore scelto sarà il colore definito nel primo intervallo che viene soddisfatto.

Nell'esempio JSON seguente entrambi gli intervalli manterranno true quando il valore *dello stato* è compreso tra 50-60. Tuttavia, il colore che verrà usato è `#343deb` perché è il primo intervallo nell'elenco che è stato soddisfatto.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Proprietà | Type | Descrizione | Obbligatoria |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject) definisce un set di condizioni di intervallo logico, che, se `true` , modificano il colore di visualizzazione dello *stato* sul colore specificato nella `color` Proprietà. Se non `range` è specificato, verrà sempre usato il colore definito nella `color` Proprietà.   | No |
| `color` | string | Colore da usare quando il valore dello stato rientra nell'intervallo. La `color` proprietà è una stringa JSON in uno dei formati seguenti: <ul><li> Valori esadecimali di tipo HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255,0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%,1)")</li><li> Nomi di colori HTML predefiniti, ad esempio giallo e blu.</li></ul> | Sì |

### <a name="rangeobject"></a>RangeObject

`RangeObject`Definisce un valore numerico dell'intervallo di un oggetto [`NumberRuleObject`](#numberruleobject) . Affinché il valore *dello stato* entri nell'intervallo, tutte le condizioni definite devono essere true.

| Proprietà | Type | Descrizione | Obbligatoria |
|-----------|----------|-------------|-------------|
| `minimum` | double | Tutti i numeri x che x ≥ `minimum` .| No |
| `maximum` | double | Tutto il numero x che x ≤ `maximum` . | No |
| `exclusiveMinimum` | double | Tutti i numeri x che x > `exclusiveMinimum` .| No |
| `exclusiveMaximum` | double | Tutti i numeri x che x < `exclusiveMaximum` .| No |

### <a name="example-of-numerictypestylerule"></a>Esempio di NumericTypeStyleRule

Il codice JSON seguente illustra uno `NumericTypeStyleRule` *stato* denominato `temperature` . In questo esempio, [`NumberRuleObject`](#numberruleobject) contiene due intervalli di temperatura definiti e i relativi stili di colore associati. Se l'intervallo di temperature è 50-69, la visualizzazione deve usare il colore `#343deb` .  Se l'intervallo di temperature è 31-70, la visualizzazione deve usare il colore `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

Un oggetto `StringTypeStyleRule` è un oggetto [`StyleObject`](#styleobject) ed è costituito dalle proprietà seguenti:

| Proprietà | Type | Descrizione | Obbligatoria |
|-----------|----------|-------------|-------------|
| `keyName` | string |  Nome della proprietà dinamica o *dello stato* .  Un oggetto `keyName` deve essere univoco all'interno della  `StyleObject` matrice.| Sì |
| `type` | string |Il valore è "String". | Sì |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| Matrice di N numero di valori di *stato* .| Sì |

### <a name="stringruleobject"></a>StringRuleObject

Un `StringRuleObject` è costituito da un numero massimo di valori di stato che corrispondono ai possibili valori stringa della proprietà di una funzionalità. Se il valore della proprietà della funzionalità non corrisponde ad alcuno dei valori di stato definiti, tale funzionalità non avrà uno stile dinamico. Se vengono specificati valori di stato duplicati, il primo ha la precedenza.

Il valore stringa corrispondente fa distinzione tra maiuscole e minuscole.

| Proprietà | Type | Descrizione | Obbligatoria |
|-----------|----------|-------------|-------------|
| `stateValue1` | string | Il colore quando la stringa del valore è stateValue1. | No |
| `stateValue2` | string | Il colore quando la stringa del valore è stateValue. | No |
| `stateValueN` | string | Il colore quando la stringa del valore è stateValueN. | No |

### <a name="example-of-stringtypestylerule"></a>Esempio di StringTypeStyleRule

Il codice JSON seguente illustra un oggetto `StringTypeStyleRule` che definisce gli stili associati a specifici tipi di riunioni.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

Un oggetto `BooleanTypeStyleRule` è un oggetto [`StyleObject`](#styleobject) ed è costituito dalle proprietà seguenti:

| Proprietà | Type | Descrizione | Obbligatoria |
|-----------|----------|-------------|-------------|
| `keyName` | string |  Nome della proprietà dinamica o *dello stato* .  Un oggetto `keyName` deve essere univoco all'interno della `StyleObject`  matrice.| Sì |
| `type` | string |Il valore è "booleano". | Sì |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)1| Coppia booleana con i colori `true` per `false` i valori di *stato* e.| Sì |

### <a name="booleanruleobject"></a>BooleanRuleObject

Un oggetto `BooleanRuleObject` definisce i colori per `true` `false` i valori e.

| Proprietà | Type | Descrizione | Obbligatoria |
|-----------|----------|-------------|-------------|
| `true` | string | Il colore da utilizzare quando il valore di *stato* è `true` . La `color` proprietà è una stringa JSON in uno dei formati seguenti: <ul><li> Valori esadecimali di tipo HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255,0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%,1)")</li><li> Nomi di colori HTML predefiniti, ad esempio giallo e blu.</li></ul>| Sì |
| `false` | string | Il colore da utilizzare quando il valore di *stato* è `false` . | Sì |

### <a name="example-of-booleantypestylerule"></a>Esempio di BooleanTypeStyleRule

Il codice JSON seguente illustra uno `BooleanTypeStyleRule` *stato* denominato `occupied` . [`BooleanRuleObject`](#booleanruleobject)Definisce i colori per `true` `false` i valori e.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```