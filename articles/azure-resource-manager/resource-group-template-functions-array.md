---
title: Funzioni del modello di Azure Resource Manager - matrici e oggetti | Microsoft Docs
description: Descrive le funzioni da usare in un modello di Azure Resource Manager per l&quot;uso di matrici e oggetti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 34fc513b6d4408e341fc5a723ca743daee39b85d
ms.contentlocale: it-it
ms.lasthandoff: 04/28/2017


---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Funzioni di matrice e oggetto per i modelli di Azure Resource Manager 

Resource Manager offre diverse funzioni per l'uso di matrici e oggetti.

* [array](#array)
* [coalesce](#coalesce)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [min](#min)
* [max](#max)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Per ottenere una matrice di valori stringa delimitata da un valore, vedere [split](resource-group-template-functions-string.md#split).

<a id="array" />

## <a name="array"></a>array
`array(convertToArray)`

Converte il valore in matrice.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| convertToArray |Sì |numero intero, stringa, matrice o oggetto |Valore da convertire in matrice. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione matrice con tipi diversi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "a"
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

### <a name="return-value"></a>Valore restituito

Una matrice.

<a id="coalesce" />

## <a name="coalesce"></a>coalesce
`coalesce(arg1, arg2, arg3, ...)`

Restituisce il primo valore non null dai parametri. Stringhe vuote, matrici vuote e oggetti vuoti sono non null.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |numero intero, stringa, matrice o oggetto |Il primo valore da controllare per verificare se è null. |
| argomenti aggiuntivi |No |numero intero, stringa, matrice o oggetto |Valori aggiuntivi da controllare per verificare se sono null. |

### <a name="examples"></a>esempi

L'esempio seguente illustra l'output per diversi usi di coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"first": null, "second": null}
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, 'fallback')]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, 1)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, parameters('objectToTest'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, array(1))]"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito

Valore dei primi parametri non null, che può essere una stringa, un numero intero, una matrice o un oggetto. Null se tutti i parametri sono null. 

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Combina più matrici e restituisce la matrice concatenata oppure combina più valori di stringa e restituisce la stringa concatenata. 

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |La prima matrice o stringa per la concatenazione. |
| argomenti aggiuntivi |No |stringa o matrice |Matrici o stringhe aggiuntive in ordine sequenziale per la concatenazione. |

Questa funzione può accettare qualsiasi numero di argomenti e può accettare stringhe o matrici per i parametri.

### <a name="examples"></a>esempi

L'esempio seguente illustra come combinare due matrici.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

L'esempio seguente illustra come combinare due valori di stringa per restituire una stringa concatenata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "[concat(parameters('prefix'), uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito
Una stringa o matrice di valori concatenati.

<a id="contains" />

## <a name="contains"></a>contains
`contains(container, itemToFind)`

Verifica se una matrice contiene un valore, se un oggetto contiene una chiave o se una stringa contiene una sottostringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| contenitore |Sì |matrice, oggetto o stringa |Valore contenente l'elemento da cercare. |
| itemToFind |Sì |stringa o numero intero |Valore da cercare. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione contains con tipi diversi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Restituisce **True** se l'elemento è stato trovato, in caso contrario restituisce **False**.

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Crea una matrice dai parametri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |Stringa, numero intero, matrice o oggetto |Primo valore della matrice. |
| argomenti aggiuntivi |No |Stringa, numero intero, matrice o oggetto |Altri valori della matrice. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione createArray con tipi diversi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Una matrice.

<a id="empty" />

## <a name="empty"></a>empty

`empty(itemToTest)`

Determina se una matrice, un oggetto o una stringa sono vuoti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| itemToTest |Sì |matrice, oggetto o stringa |Valore da controllare per verificare se è vuoto. |

### <a name="examples"></a>esempi

L'esempio seguente controlla se una matrice, un oggetto e una stringa sono vuoti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Restituisce **True** se il valore è vuoto; in caso contrario, restituisce **False**.

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Restituisce il primo elemento della matrice o il primo carattere della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Valore per recuperare il primo elemento o carattere. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la prima funzione con una matrice e una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Tipo del primo elemento in una matrice (stringa, numero intero, matrice o oggetto) o stringa del primo carattere.

<a id="intersection" />

## <a name="intersection"></a>intersezione
`intersection(arg1, arg2, arg3, ...)`

Restituisce una matrice o un oggetto singoli con gli elementi comuni dei parametri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice o oggetto |Primo valore da usare per cercare elementi comuni. |
| arg2 |Sì |matrice o oggetto |Secondo valore da usare per cercare elementi comuni. |
| argomenti aggiuntivi |No |matrice o oggetto |Valori aggiuntivi da usare per cercare elementi comuni. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare l'intersezione con matrici e oggetti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Una matrice o un oggetto con elementi comuni.

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Restituisce l'ultimo elemento della matrice o l'ultimo carattere della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Valore per recuperare l'ultimo elemento o carattere. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare l'ultima funzione con una matrice e una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Tipo dell'ultimo elemento in una matrice (stringa, numero intero, matrice o oggetto) o stringa dell'ultimo carattere.

<a id="length" />

## <a name="length"></a>length
`length(arg1)`

Restituisce il numero di elementi in una matrice o di caratteri in una stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Matrice da usare per ottenere il numero di elementi oppure stringa da usare per ottenere il numero di caratteri. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione length con una matrice e una stringa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        }
    }
}
```

È possibile usare questa funzione con una matrice per specificare il numero di iterazioni durante la creazione di risorse. Nell'esempio seguente, il parametro **siteNames** fa riferimento a una matrice di nomi da usare durante la creazione di siti Web.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Per altre informazioni sull'uso di questa funzione con una matrice, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

### <a name="return-value"></a>Valore restituito

Numero intero 

<a id="min" />

## <a name="min"></a>Min
`min(arg1)`

Restituisce il valore minimo di una matrice di numeri interi o di un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |Raccolta per ottenere il valore minimo. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione min con una matrice e un elenco di numeri interi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta il valore minimo.

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

Restituisce il valore massimo da una matrice di numeri interi o da un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |Raccolta per ottenere il valore massimo. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione max con una matrice e un elenco di numeri interi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta il valore massimo.

<a id="range" />

## <a name="range"></a>range
`range(startingInteger, numberOfElements)`

Crea una matrice di numeri interi da un numero intero iniziale, contenente un dato numero di elementi.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| startingInteger |Sì |int |Primo numero intero nella matrice. |
| numberofElements |Sì |int |Numero di valori interi della matrice. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione range:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Matrice di numeri interi.

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Restituisce una matrice con tutti gli elementi dopo il numero specificato nella matrice stessa o una stringa con tutti i caratteri dopo il numero specificato nella stringa stessa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |stringa o matrice |La stringa o la matrice da usare per i valori da ignorare. |
| numberToSkip |Sì |int |Numero di elementi o caratteri da ignorare. Se il valore è minore o uguale a 0, vengono restituiti tutti gli elementi o i caratteri nel valore. Se è maggiore della lunghezza della matrice o stringa, viene restituita una matrice o stringa vuota. |

### <a name="examples"></a>esempi

L'esempio seguente ignora il numero di elementi specificato nella matrice e il numero di caratteri specificato in una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Una stringa o matrice.

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Restituisce una matrice con il numero specificato di elementi dall'inizio della matrice, o una stringa con il numero specificato di caratteri dall'inizio della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |stringa o matrice |Stringa o matrice da cui prendere gli elementi. |
| numberToTake |Sì |int |Numero di elementi o caratteri da prendere. Se il valore è minore o uguale a 0, viene restituita una stringa o matrice vuota. Se è maggiore della lunghezza della stringa o matrice specificata, vengono restituiti tutti gli elementi nella stringa o matrice. |

### <a name="examples"></a>esempi

L'esempio seguente prende il numero specificato di elementi dalla matrice e di caratteri dalla stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="return-value"></a>Valore restituito

Una stringa o matrice.

<a id="union" />

## <a name="union"></a>union
`union(arg1, arg2, arg3, ...)`

Restituisce una matrice o un oggetto singoli con tutti gli elementi dei parametri. Valori e chiavi duplicati sono inclusi una sola volta.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice o oggetto |Primo valore da usare per l'aggiunta di elementi. |
| arg2 |Sì |matrice o oggetto |Secondo valore da usare per l'aggiunta di elementi. |
| argomenti aggiuntivi |No |matrice o oggetto |Valori aggiuntivi da usare per l'aggiunta di elementi. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare l'unione con matrici e oggetti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"four": "d", "five": "e", "six": "f"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["four", "five"]
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

### <a name="return-value"></a>Valore restituito

Una matrice o un oggetto.

## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).


