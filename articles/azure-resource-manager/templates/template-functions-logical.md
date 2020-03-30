---
title: Funzioni modello - logico
description: Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per determinare i valori logici.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156311"
---
# <a name="logical-functions-for-arm-templates"></a>Funzioni logiche per i modelli ARM

Resource Manager offre diverse funzioni per eseguire confronti nei modelli di Azure Resource Manager (ARM).

* [E](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [O](#or)

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se tutti i valori dei parametri sono true.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Primo valore da controllare per verificare se è true. |
| arg2 |Sì |boolean |Secondo valore da controllare per verificare se è true. |
| argomenti aggiuntivi |No |boolean |Argomenti aggiuntivi da controllare per verificare se sono true. |

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

| Nome | Type | valore |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Converte il parametro in un valore booleano.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
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

| Nome | Type | valore |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Restituisce un valore in base a un condizione true o false.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| condizione |Sì |boolean |Valore per verificare se è true o false. |
| trueValue |Sì | string, int, object o array |Valore da restituire quando la condizione è true. |
| falseValue |Sì | string, int, object o array |Valore da restituire quando la condizione è false. |

### <a name="return-value"></a>Valore restituito

Restituisce il secondo parametro, quando il primo parametro è **True**. In caso contrario, restituisce il terzo parametro.

### <a name="remarks"></a>Osservazioni

Quando la condizione è **True**, viene valutato solo il valore true. Quando la condizione è **False**, viene valutato solo il valore false. Con la funzione **if,** è possibile includere espressioni che sono valide solo in modo condizionale. Ad esempio, è possibile fare riferimento a una risorsa che esiste in una condizione ma non nell'altra condizione. Nella sezione seguente è illustrato un esempio di valutazione condizionale delle espressioni.

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

| Nome | Type | valore |
| ---- | ---- | ----- |
| yesOutput | string | sì |
| noOutput | string | no |
| objectOutput | Oggetto | { "test": "value1" } |

Nel [modello](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) di esempio seguente viene illustrato come utilizzare questa funzione con espressioni che sono valide solo in modo condizionale.

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

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
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

| Nome | Type | valore |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) seguente usa **not** con [equals](template-functions-comparison.md#equals).

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

| Nome | Type | valore |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>o

`or(arg1, arg2, ...)`

Verifica se uno qualsiasi dei valori dei parametri è true.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |boolean |Primo valore da controllare per verificare se è true. |
| arg2 |Sì |boolean |Secondo valore da controllare per verificare se è true. |
| argomenti aggiuntivi |No |boolean |Argomenti aggiuntivi da controllare per verificare se sono true. |

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

| Nome | Type | valore |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere Creazione di modelli di [Azure Resource Manager.](template-syntax.md)
* Per unire più modelli, vedere [Utilizzo di modelli collegati con Azure Resource Manager.](linked-templates.md)
* Per scorrere un numero specificato di volte durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Azure Resource Manager.To](copy-resources.md)iterate a specified number of times when creating a type of resource, see Create multiple instances of resources in Azure Resource Manager.
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](deploy-powershell.md).

