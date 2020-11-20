---
title: StylesObject per le mappe di Azure dinamiche
description: Guida di riferimento allo schema JSON e alla sintassi per i StylesObject usati per la creazione di mappe di Azure dinamiche.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4284956138002d209ab0934cdd052748ef8aab78
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966276"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Guida di riferimento allo schema StylesObject per mappe dinamiche

 `StylesObject`È una `StyleObject` matrice che rappresenta gli stili degli Stati. Usare il [servizio stato funzionalità](/rest/api/maps/featurestate) di Azure Maps Creator per applicare gli stili di stato alle funzionalità dei dati della mappa interna. Una volta creati gli stili dello stato e associati alle funzionalità della mappa interna, è possibile usarli per creare mappe interne dinamiche. Per altre informazioni sulla creazione di mappe interne dinamiche, vedere [implementare lo stile dinamico per le mappe interne dell'autore](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

Un oggetto `StyleObject` è di tipo [`BooleanTypeStyleRule`](#booleantypestylerule) o [`NumericTypeStyleRule`](#numerictypestylerule) .

Il codice JSON seguente mostra un oggetto `BooleanTypeStyleRule` denominato `occupied` e un oggetto `NumericTypeStyleRule` denominato `temperature` .

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
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 Un oggetto `NumericTypeStyleRule` è un oggetto  [`StyleObject`](#styleobject) ed è costituito dalle proprietà seguenti:

| Proprietà | Type | Description | Obbligatoria |
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

| Proprietà | Type | Description | Obbligatoria |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject) definisce un set di condizioni di intervallo logico, che, se `true` , modificano il colore di visualizzazione dello *stato* sul colore specificato nella `color` Proprietà. Se non `range` è specificato, verrà sempre usato il colore definito nella `color` Proprietà.   | No |
| `color` | string | Colore da usare quando il valore dello stato rientra nell'intervallo. La `color` proprietà è una stringa JSON in uno dei formati seguenti: <ul><li> Valori esadecimali di tipo HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255,0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%,1)")</li><li> Nomi di colori HTML predefiniti, ad esempio giallo e blu.</li></ul> | Sì |

### <a name="rangeobject"></a>RangeObject

`RangeObject`Definisce un valore numerico dell'intervallo di un oggetto [`NumberRuleObject`](#numberruleobject) . Affinché il valore *dello stato* entri nell'intervallo, tutte le condizioni definite devono essere true. 

| Proprietà | Type | Description | Obbligatoria |
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

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

Un oggetto `BooleanTypeStyleRule` è un oggetto [`StyleObject`](#styleobject) ed è costituito dalle proprietà seguenti:

| Proprietà | Type | Description | Obbligatoria |
|-----------|----------|-------------|-------------|
| `keyName` | string |  Nome della proprietà dinamica o *dello stato* .  Un oggetto `keyName` deve essere univoco all'interno della matrice di stile.| Sì |
| `type` | string |Il valore è "booleano". | Sì |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)1| Coppia booleana con i colori `true` per `false` i valori di *stato* e.| Sì |

### <a name="booleanruleobject"></a>BooleanRuleObject

Un oggetto `BooleanRuleObject` definisce i colori per `true` `false` i valori e.

| Proprietà | Type | Description | Obbligatoria |
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