---
title: Funzioni di confronto del modello di Azure Resource Manager | Microsoft Docs
description: "Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per confrontare valori."
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
ms.date: 06/13/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: d993bd5cae5fa2f0a6eda999c95b10bd1c268be2
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Funzioni di confronto per i modelli di Azure Resource Manager

Resource Manager include numerose funzioni per l'esecuzione di confronti nei modelli.

* [equals](#equals)
* [less](#less)
* [lessOrEquals](#lessorequals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)

<a id="equals" />

## <a name="equals"></a>equals
`equals(arg1, arg2)`

Controlla se due valori sono uguali tra loro.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |int, stringa, matrice o oggetto |Il primo valore per verificare l'uguaglianza. |
| arg2 |Sì |int, stringa, matrice o oggetto |Il secondo valore per verificare l'uguaglianza. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se i valori sono uguali; in caso contrario, restituisce **False**.

### <a name="remarks"></a>Osservazioni

La funzione uguale a viene spesso usata con l'elemento `condition` per verificare se la risorsa viene distribuita.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Esempio

Il modello di esempio controlla tipi diversi di valori per verificarne l'uguaglianza. Tutti i valori predefiniti restituiscono True.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Tipo | Valore |
| ---- | ---- | ----- |
| checkInts | Booleano | True  |
| checkStrings | Booleano | True  |
| checkArrays | Booleano | True  |
| checkObjects | Booleano | True  |

<a id="less" />

## <a name="less"></a>less
`less(arg1, arg2)`

Controlla se il primo valore è minore del secondo.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |int o stringa |Il primo valore per il confronto del minore. |
| arg2 |Sì |int o stringa |Il secondo valore per il confronto del minore. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se il primo valore è inferiore al secondo; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il modello di esempio controlla se un valore è minore dell'altro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Tipo | Valore |
| ---- | ---- | ----- |
| checkInts | Booleano | True  |
| checkStrings | Booleano | False |

<a id="lessorequals" />

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Controlla se il primo valore è minore o uguale al secondo valore.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |int o stringa |Il primo valore per il confronto del minore o dell'uguaglianza. |
| arg2 |Sì |int o stringa |Il secondo valore per il confronto del minore o dell'uguaglianza. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se il primo valore è minore o uguale al secondo; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il modello di esempio controlla se un valore è minore o uguale all'altro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Tipo | Valore |
| ---- | ---- | ----- |
| checkInts | Booleano | True  |
| checkStrings | Booleano | False |

<a id="greater" />

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Controlla se il primo valore è maggiore del secondo.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |int o stringa |Il primo valore per il confronto del maggiore. |
| arg2 |Sì |int o stringa |Il secondo valore per il confronto del maggiore. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se il primo valore è maggiore del secondo; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il modello di esempio controlla se un valore è maggiore dell'altro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Tipo | Valore |
| ---- | ---- | ----- |
| checkInts | Booleano | False |
| checkStrings | Booleano | True  |

<a id="greaterorequals" />

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Controlla se il primo valore è maggiore o uguale al secondo valore.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |int o stringa |Il primo valore per il confronto del maggiore e dell'uguaglianza. |
| arg2 |Sì |int o stringa |Il secondo valore per il confronto del maggiore e dell'uguaglianza. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se il primo valore è maggiore o uguale al secondo; in caso contrario, restituisce **False**.

### <a name="example"></a>Esempio

Il modello di esempio controlla se un valore è maggiore o uguale all'altro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Tipo | Valore |
| ---- | ---- | ----- |
| checkInts | Booleano | False |
| checkStrings | Booleano | True  |


## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).


