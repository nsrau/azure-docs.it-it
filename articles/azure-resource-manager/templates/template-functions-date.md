---
title: Funzioni modello - data
description: Descrive le funzioni da usare in un modello di Azure Resource Manager per usare le date.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986278"
---
# <a name="date-functions-for-arm-templates"></a>Funzioni di data per i modelli ARM

Resource Manager offre le funzioni seguenti per l'uso delle date nei modelli di Azure Resource Manager (ARM):

* [dateTimeAggiungi](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAggiungi

`dateTimeAdd(base, duration, [format])`

Aggiunge una durata di tempo a un valore datetime di base.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| base | Sì | string | Valore datetime iniziale per l'addizione. Utilizzare il [formato timestamp ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sì | string | Valore temporale da aggiungere alla base. Può essere un valore negativo. Utilizzare il [formato di durata ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | No | string | Formato di output per il risultato della data e dell'ora. Se non viene specificato, viene utilizzato il formato del valore di base. Utilizzare [stringhe](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) di formato standard o stringhe di [formato personalizzate.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) |

### <a name="return-value"></a>Valore restituito

Valore datetime risultante dall'aggiunta del valore duration al valore di base.

### <a name="examples"></a>Esempi

Il modello di esempio seguente mostra diversi modi per aggiungere valori di ora.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Quando il modello precedente viene distribuito `2020-04-07 14:53:14Z`con un tempo di base pari a , l'output è:

| Nome | Type | valore |
| ---- | ---- | ----- |
| add3Anni | string | 07/04/2023 2:53:14 |
| subtract9Giorni | string | 29/03/2020 2:53:14 PM |
| add1Hour (ora in stato di inade | string | 07/04/2020 3:53:14 |

Il modello di esempio successivo mostra come impostare l'ora di inizio per una pianificazione di automazione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Restituisce il valore datetime corrente (UTC) nel formato specificato. Se non viene fornito alcun formato, viene utilizzato il formato ISO 8601 (yyyyMddTHHmmss). **Questa funzione può essere utilizzata solo nel valore predefinito per un parametro.**

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| format |No |string |Valore URI codificato da convertire in stringa. Utilizzare [stringhe](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) di formato standard o stringhe di [formato personalizzate.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) |

### <a name="remarks"></a>Osservazioni

È possibile utilizzare questa funzione solo all'interno di un'espressione per il valore predefinito di un parametro. L'utilizzo di questa funzione in qualsiasi altro punto di un modello restituisce un errore. La funzione non è consentita in altre parti del modello perché restituisce un valore diverso ogni volta che viene chiamato. La distribuzione dello stesso modello con gli stessi parametri non produrrebbe in modo affidabile gli stessi risultati.

Se si utilizza l'opzione per ridistribuire una [distribuzione con esito positivo precedente](rollback-on-error.md)e la distribuzione precedente include un parametro che utilizza utcNow, il parametro non viene rivalutato. Al contrario, il valore del parametro dalla distribuzione precedente viene riutilizzato automaticamente nella distribuzione di rollback.

Prestare attenzione alla ridistribuzione di un modello che si basa sulla funzione utcNow per un valore predefinito. Quando si ridistribuisce e non si specifica un valore per il parametro, la funzione viene rivalutata. Se si desidera aggiornare una risorsa esistente anziché crearne una nuova, passare il valore del parametro dalla distribuzione precedente.

### <a name="return-value"></a>Valore restituito

Valore datetime UTC corrente.

### <a name="examples"></a>Esempi

Il modello di esempio seguente mostra formati diversi per il valore datetime.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

L'output dell'esempio precedente varia per ogni distribuzione, ma sarà simile al seguente:The output from the preceding example varies for each deployment but will be similar to:

| Nome | Type | valore |
| ---- | ---- | ----- |
| utcOutput (informazioni in questo campo) | string | 20190305T1753318 |
| utcShortOutput | string | 05/03/2019 |
| utcCustomOutput | string | 3 5 |

L'esempio seguente mostra come usare un valore dalla funzione quando si imposta un valore di tag.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```