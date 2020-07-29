---
title: Funzioni di modello-matrici
description: Descrive le funzioni da utilizzare in un modello di Azure Resource Manager per l'utilizzo di matrici.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 4d4ee96888aee5421d88b5371ac25a69c0af4fd7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677849"
---
# <a name="array-functions-for-arm-templates"></a>Funzioni di matrice per i modelli ARM

Gestione risorse offre diverse funzioni per l'uso di matrici nel modello di Azure Resource Manager (ARM).

* [array](#array)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Per ottenere una matrice di valori stringa delimitata da un valore, vedere [split](template-functions-string.md#split).

## <a name="array"></a>array

`array(convertToArray)`

Converte il valore in matrice.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| convertToArray |Sì |int, stringa, matrice o oggetto |Valore da convertire in matrice. |

### <a name="return-value"></a>Valore restituito

Matrice .

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) seguente mostra come usare la funzione matrice con tipi diversi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "efgh"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| intOutput | Array |  [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Combina più matrici e restituisce la matrice concatenata oppure combina più valori di stringa e restituisce la stringa concatenata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |La prima matrice o stringa per la concatenazione. |
| argomenti aggiuntivi |No |stringa o matrice |Matrici o stringhe aggiuntive in ordine sequenziale per la concatenazione. |

Questa funzione può accettare qualsiasi numero di argomenti e può accettare stringhe o matrici per i parametri. Tuttavia, non è possibile fornire sia matrici che stringhe per i parametri. Le matrici sono concatenate solo con altre matrici.

### <a name="return-value"></a>Valore restituito

Stringa o matrice di valori concatenati.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) seguente illustra come combinare due matrici.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstArray": {
            "type": "array",
            "defaultValue": [
                "1-1",
                "1-2",
                "1-3"
            ]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": [
                "2-1",
                "2-2",
                "2-3"
            ]
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) seguente illustra come combinare due valori stringa e restituisce una stringa concatenata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| concatOutput | string | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Verifica se una matrice contiene un valore, se un oggetto contiene una chiave o se una stringa contiene una sottostringa. Il confronto fra stringhe fa distinzione tra maiuscole e minuscole. Tuttavia, quando si testa se un oggetto contiene una chiave, il confronto non fa distinzione tra maiuscole e minuscole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| contenitore |Sì |matrice, oggetto o stringa |Valore che contiene il valore da trovare. |
| itemToFind |Sì |stringa o numero intero |Valore da trovare. |

### <a name="return-value"></a>Valore restituito

**True** se l'elemento viene individuato; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) seguente mostra come usare la funzione contains con tipi diversi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createarray"></a>createarray

`createArray (arg1, arg2, arg3, ...)`

Crea una matrice dai parametri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |Stringa, numero intero, matrice o oggetto |Primo valore della matrice. |
| argomenti aggiuntivi |No |Stringa, numero intero, matrice o oggetto |Altri valori della matrice. |

### <a name="return-value"></a>Valore restituito

Matrice .

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) seguente mostra come usare la funzione createArray con tipi diversi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |

## <a name="empty"></a>empty

`empty(itemToTest)`

Determina se una matrice, un oggetto o una stringa sono vuoti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| itemToTest |Sì |matrice, oggetto o stringa |Valore da controllare per verificare se è vuoto. |

### <a name="return-value"></a>Valore restituito

**True** se il valore è vuoto; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) seguente controlla se una matrice, un oggetto e una stringa sono vuoti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

Restituisce il primo elemento della matrice o il primo carattere della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Valore per recuperare il primo elemento o carattere. |

### <a name="return-value"></a>Valore restituito

Il tipo (string, int, array o object) del primo elemento di una matrice o il primo carattere di una stringa.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) seguente mostra come usare la prima funzione con una matrice e una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | string | one |
| stringOutput | string | O |

## <a name="intersection"></a>intersezione

`intersection(arg1, arg2, arg3, ...)`

Restituisce una matrice o un oggetto singoli con gli elementi comuni dei parametri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice o oggetto |Primo valore da usare per cercare elementi comuni. |
| arg2 |Sì |matrice o oggetto |Secondo valore da usare per cercare elementi comuni. |
| argomenti aggiuntivi |No |matrice o oggetto |Valori aggiuntivi da usare per cercare elementi comuni. |

### <a name="return-value"></a>Valore restituito

Una matrice o un oggetto con elementi comuni.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) seguente mostra come usare l'intersezione con matrici e oggetti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| objectOutput | Oggetto | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

Restituisce l'ultimo elemento della matrice o l'ultimo carattere della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Valore per recuperare l'ultimo elemento o carattere. |

### <a name="return-value"></a>Valore restituito

Il tipo (string, int, array o object) dell'ultimo elemento di una matrice o l'ultimo carattere di una stringa.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) seguente mostra come usare l'ultima funzione con una matrice e una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | string | three |
| stringOutput | string | e |

## <a name="length"></a>length

`length(arg1)`

Restituisce il numero di elementi in una matrice, i caratteri di una stringa o le proprietà a livello di radice in un oggetto.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |Array, String o Object |Matrice da usare per ottenere il numero di elementi, la stringa da usare per ottenere il numero di caratteri o l'oggetto da usare per ottenere il numero di proprietà a livello di radice. |

### <a name="return-value"></a>Valore restituito

Numero intero

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) seguente mostra come usare la funzione length con una matrice e una stringa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

È possibile usare questa funzione con una matrice per specificare il numero di iterazioni durante la creazione di risorse. Nell'esempio seguente, il parametro **siteNames** fa riferimento a una matrice di nomi da usare durante la creazione di siti Web.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Per altre informazioni sull'uso di questa funzione con una matrice, vedere [Creare più istanze di risorse in Gestione risorse di Azure](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Restituisce il valore massimo da una matrice di numeri interi o da un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore massimo. |

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta il valore massimo.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) seguente mostra come usare la funzione max con una matrice e un elenco di numeri interi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min

`min(arg1)`

Restituisce il valore minimo di una matrice di numeri interi o di un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore minimo. |

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta il valore minimo.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) seguente mostra come usare la funzione min con una matrice e un elenco di numeri interi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Crea una matrice di numeri interi da un numero intero iniziale, contenente un dato numero di elementi.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| startIndex |Sì |INT |Primo numero intero nella matrice. La somma di startIndex e count non deve essere maggiore di 2147483647. |
| count |Sì |INT |Numero di valori interi della matrice. Deve essere un numero intero non negativo fino a 10000. |

### <a name="return-value"></a>Valore restituito

Matrice di numeri interi.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) seguente illustra come usare la funzione range:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Restituisce una matrice con tutti gli elementi dopo il numero specificato nella matrice stessa o una stringa con tutti i caratteri dopo il numero specificato nella stringa stessa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |stringa o matrice |Stringa o matrice da usare per i valori da ignorare. |
| numberToSkip |Sì |INT |Numero di elementi o caratteri da ignorare. Se il valore è minore o uguale a 0, vengono restituiti tutti gli elementi o i caratteri nel valore. Se il valore è maggiore della lunghezza della stringa o della matrice, viene restituita una stringa o una matrice vuota. |

### <a name="return-value"></a>Valore restituito

Stringa o matrice.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) seguente ignora il numero di elementi specificato nella matrice e il numero di caratteri specificato in una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | string | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Restituisce una matrice con il numero specificato di elementi dall'inizio della matrice, o una stringa con il numero specificato di caratteri dall'inizio della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |stringa o matrice |Stringa o matrice da cui prendere gli elementi. |
| numberToTake |Sì |INT |Numero di elementi o caratteri da prendere. Se il valore è minore o uguale a 0, viene restituita una stringa o un matrice vuota. Se il valore è maggiore della lunghezza della stringa o matrice specificata, vengono restituiti tutti gli elementi nella stringa o nella matrice. |

### <a name="return-value"></a>Valore restituito

Stringa o matrice.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) seguente prende il numero specificato di elementi dalla matrice e di caratteri dalla stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | string | on |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Restituisce una matrice o un oggetto singoli con tutti gli elementi dei parametri. Valori e chiavi duplicati sono inclusi una sola volta.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice o oggetto |Primo valore da usare per l'aggiunta di elementi. |
| arg2 |Sì |matrice o oggetto |Secondo valore da usare per l'aggiunta di elementi. |
| argomenti aggiuntivi |No |matrice o oggetto |Valori aggiuntivi da usare per l'aggiunta di elementi. |

### <a name="return-value"></a>Valore restituito

Una matrice o un oggetto.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) seguente mostra come usare l'intersezione con matrici e oggetti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| objectOutput | Oggetto | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
