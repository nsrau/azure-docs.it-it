---
title: Funzioni di modello-numeric
description: Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per elaborare i numeri.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: dc15ade453fc5ea4dc031ced0377892f4f8cf27d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192349"
---
# <a name="numeric-functions-for-arm-templates"></a>Funzioni numeriche per i modelli ARM

Gestione risorse fornisce le funzioni seguenti per l'utilizzo di numeri interi nel modello di Azure Resource Manager (ARM):

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [Mul](#mul)
* [Sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

Restituisce la somma dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
|operand1 |Sì |INT |Il primo numero da aggiungere. |
|operand2 |Sì |INT |Il secondo numero da aggiungere. |

### <a name="return-value"></a>Valore restituito

Un intero che contiene la somma dei parametri.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) seguente aggiunge due parametri.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Restituisce l'indice di un ciclo di iterazione.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| loopName | No | stringa | Nome del ciclo per ottenere l'iterazione. |
| offset |No |INT |Il numero da aggiungere al valore di iterazione in base zero. |

### <a name="remarks"></a>Osservazioni

Questa funzione viene sempre usata con un oggetto **copy** . Se non viene specificato alcun valore per **offset**, viene restituito il valore di iterazione corrente. Il valore di iterazione inizia da zero.

La proprietà **loopName** consente di specificare se copyIndex fa riferimento all'iterazione di una risorsa o all'iterazione di una proprietà. Se non viene specificato alcun valore per **loopName**, viene usata l'iterazione del tipo di risorsa corrente. Specificare un valore per **loopName** durante l'iterazione di una proprietà.

Per ulteriori informazioni sull'utilizzo di Copy, vedere:

* [Iterazione delle risorse nei modelli ARM](copy-resources.md)
* [Iterazione delle proprietà nei modelli ARM](copy-properties.md)
* [Iterazione delle variabili nei modelli ARM](copy-variables.md)
* [Iterazione di output nei modelli ARM](copy-outputs.md)

### <a name="example"></a>Esempio

L'esempio seguente illustra un ciclo di copy e il valore di indice incluso nel nome.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta l'indice corrente dell'iterazione.

## <a name="div"></a>div

`div(operand1, operand2)`

Restituisce la divisione Integer dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |INT |Il numero da dividere. |
| operand2 |Sì |INT |Il numero usato per dividere. Non può essere 0. |

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta la divisione.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) seguente mostra come dividere un parametro per un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>float

`float(arg1)`

Converte il valore in un numero a virgola mobile. Usare questa funzione solo quando si passano parametri personalizzati a un'applicazione, ad esempio un'app per la logica.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o numero intero |Il valore da convertire in un numero a virgola mobile. |

### <a name="return-value"></a>Valore restituito

Un numero a virgola mobile.

### <a name="example"></a>Esempio

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

## <a name="int"></a>INT

`int(valueToConvert)`

Converte il valore specificato in un numero intero.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sì |stringa o numero intero |Il valore da convertire in numero intero. |

### <a name="return-value"></a>Valore restituito

Un intero del valore convertito.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) seguente converte il valore del parametro fornito dall'utente in intero.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": {
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

Restituisce il valore massimo da una matrice di numeri interi o da un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore massimo. |

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta il valore massimo dalla raccolta.

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

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min (arg1)`

Restituisce il valore minimo di una matrice di numeri interi o di un elenco di numeri interi delimitato da virgole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |matrice di numeri interi o elenco di numeri interi delimitato da virgole |La raccolta per ottenere il valore minimo. |

### <a name="return-value"></a>Valore restituito

un intero che rappresenta il valore minimo dalla raccolta.

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

| Nome | Type | valore |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Restituisce la parte rimanente della divisione Integer usando i due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |INT |Il numero da dividere. |
| operand2 |Sì |INT |Il numero usato per dividere, non può essere 0. |

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta il resto.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) seguente restituisce il resto della divisione di un parametro per un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Restituisce la moltiplicazione dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |INT |Il primo numero da moltiplicare. |
| operand2 |Sì |INT |Il secondo numero da moltiplicare. |

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta la moltiplicazione.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) seguente mostra come moltiplicare un parametro per un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Restituisce la sottrazione dei due numeri interi forniti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| operand1 |Sì |INT |Il numero da cui sottrarre. |
| operand2 |Sì |INT |Il numero sottratto. |

### <a name="return-value"></a>Valore restituito

Un intero che rappresenta la sottrazione.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) seguente mostra come sottrarre un parametro da un altro parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per eseguire l'iterazione di un numero specificato di volte durante la creazione di un tipo di risorsa, vedere [creare più istanze di risorse in Azure Resource Manager](copy-resources.md).
