---
title: Funzioni di modello-Logical
description: Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per determinare i valori logici.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: df8433d167a166fe94d965f81e42cd0b3e8f0e54
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150702"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Funzioni logiche nei modelli di Azure Resource Manager

Resource Manager include numerose funzioni per l'esecuzione di confronti nei modelli.

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se tutti i valori dei parametri sono true.

### <a name="parameters"></a>parametri

| . | obbligatori | digitare | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Primo valore da controllare per verificare se è true. |
| arg2 |Sì |boolean |Secondo valore da controllare per verificare se è true. |
| argomenti aggiuntivi |No |boolean |Argomenti aggiuntivi da controllare per verificare se sono true. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se tutti i valori sono true. In caso contrario, restituisce **False**.

### <a name="examples"></a>esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) seguente mostra come usare le funzioni logiche.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

L'output dell'esempio precedente è:

| Nome | digitare | Valore |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Converte il parametro in un valore booleano.

### <a name="parameters"></a>parametri

| . | obbligatori | digitare | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o int |Valore da convertire in un valore booleano. |

### <a name="return-value"></a>Valore restituito
Valore booleano del valore convertito.

### <a name="examples"></a>esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) seguente illustra come usare il parametro bool con un numero intero o una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è:

| Nome | digitare | Valore |
| ---- | ---- | ----- |
| trueString | Bool | true |
| falseString | Bool | False |
| trueInt | Bool | true |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Restituisce un valore in base a un condizione true o false.

### <a name="parameters"></a>parametri

| . | obbligatori | digitare | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| condition |Sì |boolean |Valore per verificare se è true o false. |
| trueValue |Sì | string, int, object o array |Valore da restituire quando la condizione è true. |
| falseValue |Sì | string, int, object o array |Valore da restituire quando la condizione è false. |

### <a name="return-value"></a>Valore restituito

Restituisce il secondo parametro, quando il primo parametro è **True**. In caso contrario, restituisce il terzo parametro.

### <a name="remarks"></a>Osservazioni

Quando la condizione è **true**, viene valutato solo il valore true. Quando la condizione è **false**, viene valutato solo il valore false. Con la funzione **if** è possibile includere espressioni che sono valide solo in modo condizionale. Ad esempio, è possibile fare riferimento a una risorsa presente in una condizione ma non in un'altra condizione. Nella sezione seguente viene illustrato un esempio della valutazione condizionale delle espressioni.

### <a name="examples"></a>esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) seguente illustra come usare la funzione `if`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

L'output dell'esempio precedente è:

| Nome | digitare | Valore |
| ---- | ---- | ----- |
| yesOutput | String | yes |
| noOutput | String | no |
| objectOutput | Object | { "test": "value1" } |

Nel [modello di esempio](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) seguente viene illustrato come utilizzare questa funzione con espressioni che sono valide solo in modo condizionale.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

Converte il valore booleano nel valore opposto.

### <a name="parameters"></a>parametri

| . | obbligatori | digitare | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Valore da convertire. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** quando il parametro è **False**. Restituisce **False** quando il parametro è **True**.

### <a name="examples"></a>esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) seguente mostra come usare le funzioni logiche.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

L'output dell'esempio precedente è:

| Nome | digitare | Valore |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) seguente usa **not** con [equals](resource-group-template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

L'output dell'esempio precedente è:

| Nome | digitare | Valore |
| ---- | ---- | ----- |
| checkNotEquals | Bool | true |

## <a name="or"></a>oppure

`or(arg1, arg2, ...)`

Verifica se uno qualsiasi dei valori dei parametri è true.

### <a name="parameters"></a>parametri

| . | obbligatori | digitare | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Primo valore da controllare per verificare se è true. |
| arg2 |Sì |boolean |Secondo valore da controllare per verificare se è true. |
| argomenti aggiuntivi |No |boolean |Argomenti aggiuntivi da controllare per verificare se sono true. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se uno qualsiasi dei valori è true. In caso contrario, restituisce **False**.

### <a name="examples"></a>esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) seguente mostra come usare le funzioni logiche.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

L'output dell'esempio precedente è:

| Nome | digitare | Valore |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).

