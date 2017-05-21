---
title: Funzioni numeriche del modello di Azure Resource Manager | Microsoft Docs
description: "Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per elaborare i numeri."
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
ms.date: 05/08/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 66984bef9e82df80818eea31bd37de524b567b33
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Funzioni numeriche per i modelli di Azure Resource Manager

Gestione risorse fornisce le funzioni seguenti per usare i numeri interi:

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>add
`add(operand1, operand2)`

Restituisce la somma dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- | 
|operand1 |Sì |int |Il primo numero da aggiungere. |
|operand2 |Sì |int |Il secondo numero da aggiungere. |

### <a name="examples"></a>esempi

L'esempio seguente aggiunge due parametri.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito

Un intero che contiene la somma dei parametri.

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Restituisce l'indice di un ciclo di iterazione. 

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| loopName | No | string | Nome del ciclo per ottenere l'iterazione. |
| offset |No |int |Il numero da aggiungere al valore di iterazione in base zero. |

### <a name="remarks"></a>Osservazioni

Questa funzione viene sempre usata con un oggetto **copy** . Se non viene specificato alcun valore per **offset**, viene restituito il valore di iterazione corrente. Il valore di iterazione inizia da zero.

La proprietà **loopName** consente di specificare se copyIndex fa riferimento all'iterazione di una risorsa o all'iterazione di una proprietà. Se non viene specificato alcun valore per **loopName**, viene usata l'iterazione del tipo di risorsa corrente. Specificare un valore per **loopName** durante l'iterazione di una proprietà. 
 
Per una descrizione completa dell'uso di **copyIndex**, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md).

### <a name="examples"></a>esempi

L'esempio seguente illustra un ciclo di copy e il valore di indice incluso nel nome. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta l'indice corrente dell'iterazione.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Restituisce la divisione Integer dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |int |Il numero da dividere. |
| operand2 |Sì |int |Il numero usato per dividere. Non può essere 0. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come dividere un parametro per un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta la divisione.

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Converte il valore in un numero a virgola mobile. Usare questa funzione solo quando si passano parametri personalizzati a un'applicazione, ad esempio un'app per la logica.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o int |Il valore da convertire in un numero a virgola mobile. |

### <a name="examples"></a>esempi

L'esempio seguente illustra come usare float per passare parametri a un'app per la logica:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
        "custom1": {
            "value": "[float('3.0')]"
        },
        "custom2": {
            "value": "[float(3)]"
        },
```

### <a name="return-value"></a>Valore restituito
Un numero a virgola mobile.

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Converte il valore specificato in un numero intero.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sì |stringa o int |Il valore da convertire in numero intero. |

### <a name="examples"></a>esempi

L'esempio seguente converte il valore del parametro fornito dall'utente in intero.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[variables('intValue')]"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito

Un intero.

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

Restituisce il valore minimo di una matrice di numeri interi o di un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore minimo. |

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

un intero che rappresenta il valore minimo dalla raccolta.

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Restituisce il valore massimo da una matrice di numeri interi o da un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore massimo. |

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

Un intero che rappresenta il valore massimo dalla raccolta.

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Restituisce la parte rimanente della divisione Integer usando i due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |int |Il numero da dividere. |
| operand2 |Sì |int |Il numero usato per dividere; non può corrispondere a 0. |

### <a name="examples"></a>esempi

L'esempio seguente restituisce il resto della divisione di un parametro per un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito
Un intero che rappresenta il resto.

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Restituisce la moltiplicazione dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |int |Il primo numero da moltiplicare. |
| operand2 |Sì |int |Il secondo numero da moltiplicare. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come moltiplicare un parametro per un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta la moltiplicazione.

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Restituisce la sottrazione dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |int |Il numero da cui sottrarre. |
| operand2 |Sì |int |Il numero sottratto. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come sottrarre un parametro da un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito
Un intero che rappresenta la sottrazione.

## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).


