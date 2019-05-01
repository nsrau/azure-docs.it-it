---
title: 'Funzioni del modello di Azure Resource Manager: logiche | Microsoft Docs'
description: Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per determinare i valori logici.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2ccdd337d5c01a0ac0253fe1d1e131fa4e6d51a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782991"
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

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Primo valore da controllare per verificare se è true. |
| arg2 |Sì |boolean |Secondo valore da controllare per verificare se è true. |
| argomenti aggiuntivi |No  |boolean |Argomenti aggiuntivi da controllare per verificare se sono true. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se tutti i valori sono true. In caso contrario, restituisce **False**.

### <a name="examples"></a>Esempi

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

| NOME | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True  |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Converte il parametro in un valore booleano.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |stringa o numero intero |Valore da convertire in un valore booleano. |

### <a name="return-value"></a>Valore restituito
Valore booleano del valore convertito.

### <a name="examples"></a>Esempi

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

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| trueString | Bool | True  |
| falseString | Bool | False |
| trueInt | Bool | True  |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Restituisce un valore in base a un condizione true o false.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| condition |Sì |boolean |Valore da controllare se è true o false. |
| trueValue |Sì | string, int, object o array |Valore da restituire quando la condizione è true. |
| falseValue |Sì | string, int, object o array |Valore da restituire quando la condizione è false. |

### <a name="return-value"></a>Valore restituito

Restituisce il secondo parametro, quando il primo parametro è **True**. In caso contrario, restituisce il terzo parametro.

### <a name="remarks"></a>Osservazioni

Quando la condizione è **True**, viene valutato solo il valore true. Quando la condizione è **False**, viene valutato solo il valore false. Con il **se** (funzione), è possibile includere le espressioni in modo condizionale solo validi. Ad esempio, è possibile fare riferimento a una risorsa esistente in una condizione, ma non nell'altra condizione. Nella sezione seguente è illustrato un esempio di in modo condizionale la valutazione delle espressioni.

### <a name="examples"></a>Esempi

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

| NOME | Type | Value |
| ---- | ---- | ----- |
| yesOutput | string | Sì |
| noOutput | string | no |
| objectOutput | Object | { "test": "value1" } |

Quanto segue [modello di esempio](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) viene illustrato come utilizzare questa funzione con le espressioni in modo condizionale solo validi.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Valore da convertire. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** quando il parametro è **False**. Restituisce **False** quando il parametro è **True**.

### <a name="examples"></a>Esempi

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

| NOME | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True  |
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

| NOME | Type | Value |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True  |

## <a name="or"></a>oppure

`or(arg1, arg2, ...)`

Verifica se uno qualsiasi dei valori dei parametri è true.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Primo valore da controllare per verificare se è true. |
| arg2 |Sì |boolean |Secondo valore da controllare per verificare se è true. |
| argomenti aggiuntivi |No  |boolean |Argomenti aggiuntivi da controllare per verificare se sono true. |

### <a name="return-value"></a>Valore restituito

Restituisce **True** se uno qualsiasi dei valori è true. In caso contrario, restituisce **False**.

### <a name="examples"></a>Esempi

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

| NOME | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True  |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).

