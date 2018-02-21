---
title: Funzioni del modello di Azure Resource Manager - matrici e oggetti | Microsoft Docs
description: Descrive le funzioni da usare in un modello di Azure Resource Manager per l'uso di matrici e oggetti.
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
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 7d040fe55cb46665c97668a76ccbc66adc002f89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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
* [json](#json)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
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

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| convertToArray |Sì |int, stringa, matrice o oggetto |Valore da convertire in matrice. |

### <a name="return-value"></a>Valore restituito

Una matrice.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) seguente mostra come usare la funzione matrice con tipi diversi.

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["a"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

<a id="coalesce" />

## <a name="coalesce"></a>coalesce
`coalesce(arg1, arg2, arg3, ...)`

Restituisce il primo valore non null dai parametri. Stringhe vuote, matrici vuote e oggetti vuoti sono non null.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |int, stringa, matrice o oggetto |Il primo valore da controllare per verificare se è null. |
| argomenti aggiuntivi |No  |numero intero, stringa, matrice o oggetto |Valori aggiuntivi da controllare per verificare se sono null. |

### <a name="return-value"></a>Valore restituito

Valore dei primi parametri non null, che può essere una stringa, un numero intero, una matrice o un oggetto. Null se tutti i parametri sono null. 

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) seguente illustra l'output per diversi usi di coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null, 
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| stringOutput | string | default |
| intOutput | int | 1 |
| objectOutput | Oggetto | {"first": "default"} |
| arrayOutput | Array | [1] |
| emptyOutput | Booleano | True  |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Combina più matrici e restituisce la matrice concatenata oppure combina più valori di stringa e restituisce la stringa concatenata. 

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |La prima matrice o stringa per la concatenazione. |
| argomenti aggiuntivi |No  |stringa o matrice |Matrici o stringhe aggiuntive in ordine sequenziale per la concatenazione. |

Questa funzione può accettare qualsiasi numero di argomenti e può accettare stringhe o matrici per i parametri.

### <a name="return-value"></a>Valore restituito
Stringa o matrice di valori concatenati.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) seguente illustra come combinare due matrici.

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) seguente illustra come combinare due valori stringa e restituisce una stringa concatenata.

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
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| concatOutput | string | prefix-5yj4yjf5mbg72 |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

<a id="contains" />

## <a name="contains"></a>contains
`contains(container, itemToFind)`

Verifica se una matrice contiene un valore, se un oggetto contiene una chiave o se una stringa contiene una sottostringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| Contenitore |Sì |matrice, oggetto o stringa |Valore che contiene il valore da trovare. |
| itemToFind |Sì |stringa o numero intero |Valore da trovare. |

### <a name="return-value"></a>Valore restituito

**True** se l'elemento viene individuato; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) seguente mostra come usare la funzione contains con tipi diversi:

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| stringTrue | Booleano | True  |
| stringFalse | Booleano | False |
| objectTrue | Booleano | True  |
| objectFalse | Booleano | False |
| arrayTrue | Booleano | True  |
| arrayFalse | Booleano | False |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Crea una matrice dai parametri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |Stringa, numero intero, matrice o oggetto |Primo valore della matrice. |
| argomenti aggiuntivi |No  |Stringa, numero intero, matrice o oggetto |Altri valori della matrice. |

### <a name="return-value"></a>Valore restituito

Una matrice.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) seguente mostra come usare la funzione createArray con tipi diversi:

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

<a id="empty" />

## <a name="empty"></a>empty

`empty(itemToTest)`

Determina se una matrice, un oggetto o una stringa sono vuoti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| itemToTest |Sì |matrice, oggetto o stringa |Valore da controllare per verificare se è vuoto. |

### <a name="return-value"></a>Valore restituito

**True** se il valore è vuoto; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) seguente controlla se una matrice, un oggetto e una stringa sono vuoti.

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayEmpty | Booleano | True  |
| objectEmpty | Booleano | True  |
| stringEmpty | Booleano | True  |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Restituisce il primo elemento della matrice o il primo carattere della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Valore per recuperare il primo elemento o carattere. |

### <a name="return-value"></a>Valore restituito

Il tipo (string, int, array o object) del primo elemento di una matrice o il primo carattere di una stringa.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) seguente mostra come usare la prima funzione con una matrice e una stringa.

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayOutput | string | one |
| stringOutput | string | O |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

<a id="intersection" />

## <a name="intersection"></a>intersezione
`intersection(arg1, arg2, arg3, ...)`

Restituisce una matrice o un oggetto singoli con gli elementi comuni dei parametri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice o oggetto |Primo valore da usare per cercare elementi comuni. |
| arg2 |Sì |matrice o oggetto |Secondo valore da usare per cercare elementi comuni. |
| argomenti aggiuntivi |No  |matrice o oggetto |Valori aggiuntivi da usare per cercare elementi comuni. |

### <a name="return-value"></a>Valore restituito

Una matrice o un oggetto con elementi comuni.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) seguente mostra come usare l'intersezione con matrici e oggetti:

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| objectOutput | Oggetto | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json
`json(arg1)`

Restituisce un oggetto JSON.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa |Valore da convertire in JSON. |


### <a name="return-value"></a>Valore restituito

Oggetto JSON dalla stringa specificata o un oggetto vuoto quando viene specificato **null**.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) seguente mostra come usare la funzione json con matrici e oggetti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "jsonOutput": {
            "type": "object",
            "value": "[json('{\"a\": \"b\"}')]"
        },
        "nullOutput": {
            "type": "bool",
            "value": "[empty(json('null'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| jsonOutput | Oggetto | {"a": "b"} |
| nullOutput | boolean | True  |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Restituisce l'ultimo elemento della matrice o l'ultimo carattere della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Valore per recuperare l'ultimo elemento o carattere. |

### <a name="return-value"></a>Valore restituito

Il tipo (string, int, array o object) dell'ultimo elemento di una matrice o l'ultimo carattere di una stringa.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) seguente mostra come usare l'ultima funzione con una matrice e una stringa.

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayOutput | string | three |
| stringOutput | string | e |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="length" />

## <a name="length"></a>length
`length(arg1)`

Restituisce il numero di elementi in una matrice o di caratteri in una stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o matrice |Matrice da usare per ottenere il numero di elementi oppure stringa da usare per ottenere il numero di caratteri. |

### <a name="return-value"></a>Valore restituito

Numero intero 

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) seguente mostra come usare la funzione length con una matrice e una stringa:

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

È possibile usare questa funzione con una matrice per specificare il numero di iterazioni durante la creazione di risorse. Nell'esempio seguente, il parametro **siteNames** fa riferimento a una matrice di nomi da usare durante la creazione di siti Web.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Per altre informazioni sull'uso di questa funzione con una matrice, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

Restituisce il valore massimo da una matrice di numeri interi o da un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore massimo. |

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta il valore massimo.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) seguente mostra come usare la funzione max con una matrice e un elenco di numeri interi:

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>Min
`min(arg1)`

Restituisce il valore minimo di una matrice di numeri interi o di un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore minimo. |

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta il valore minimo.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) seguente mostra come usare la funzione min con una matrice e un elenco di numeri interi:

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="range" />

## <a name="range"></a>range
`range(startingInteger, numberOfElements)`

Crea una matrice di numeri interi da un numero intero iniziale, contenente un dato numero di elementi.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| startingInteger |Sì |int |Primo numero intero nella matrice. |
| numberofElements |Sì |int |Numero di valori interi della matrice. |

### <a name="return-value"></a>Valore restituito

Matrice di numeri interi.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) seguente illustra come usare la funzione range:

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Restituisce una matrice con tutti gli elementi dopo il numero specificato nella matrice stessa o una stringa con tutti i caratteri dopo il numero specificato nella stringa stessa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |stringa o matrice |Stringa o matrice da usare per i valori da ignorare. |
| numberToSkip |Sì |int |Numero di elementi o caratteri da ignorare. Se il valore è minore o uguale a 0, vengono restituiti tutti gli elementi o i caratteri nel valore. Se il valore è maggiore della lunghezza della stringa o della matrice, viene restituita una stringa o una matrice vuota. |

### <a name="return-value"></a>Valore restituito

Stringa o matrice.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) seguente ignora il numero di elementi specificato nella matrice e il numero di caratteri specificato in una stringa.

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | string | two three |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Restituisce una matrice con il numero specificato di elementi dall'inizio della matrice, o una stringa con il numero specificato di caratteri dall'inizio della stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |stringa o matrice |Stringa o matrice da cui prendere gli elementi. |
| numberToTake |Sì |int |Numero di elementi o caratteri da prendere. Se il valore è minore o uguale a 0, viene restituita una stringa o un matrice vuota. Se il valore è maggiore della lunghezza della stringa o matrice specificata, vengono restituiti tutti gli elementi nella stringa o nella matrice. |

### <a name="return-value"></a>Valore restituito

Stringa o matrice.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) seguente prende il numero specificato di elementi dalla matrice e di caratteri dalla stringa.

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | string | in |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="union" />

## <a name="union"></a>union
`union(arg1, arg2, arg3, ...)`

Restituisce una matrice o un oggetto singoli con tutti gli elementi dei parametri. Valori e chiavi duplicati sono inclusi una sola volta.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice o oggetto |Primo valore da usare per l'aggiunta di elementi. |
| arg2 |Sì |matrice o oggetto |Secondo valore da usare per l'aggiunta di elementi. |
| argomenti aggiuntivi |No  |matrice o oggetto |Valori aggiuntivi da usare per l'aggiunta di elementi. |

### <a name="return-value"></a>Valore restituito

Una matrice o un oggetto.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) seguente mostra come usare l'intersezione con matrici e oggetti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| NOME | type | Valore |
| ---- | ---- | ----- |
| objectOutput | Oggetto | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).

