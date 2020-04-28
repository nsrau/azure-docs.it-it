---
title: Funzioni modello-data
description: Descrive le funzioni da usare in un modello di Azure Resource Manager per lavorare con le date.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0c31b26361a262a502b2a9e0fb068391846cab4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192298"
---
# <a name="date-functions-for-arm-templates"></a>Funzioni di data per i modelli ARM

Gestione risorse fornisce le funzioni seguenti per l'utilizzo delle date nei modelli di Azure Resource Manager (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Aggiunge una durata di tempo a un valore di base. È previsto il formato ISO 8601.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| base | Sì | stringa | Valore DateTime iniziale per l'addizione. Usare il [formato timestamp ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sì | stringa | Valore di ora da aggiungere alla base. Può essere un valore negativo. Usare il [formato di durata ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | No | stringa | Formato di output per il risultato della data e ora. Se non viene specificato, viene usato il formato del valore di base. Utilizzare [stringhe di formato standard](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [stringhe di formato personalizzate](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valore restituito

Valore DateTime risultante dall'aggiunta del valore Duration al valore di base.

### <a name="examples"></a>Esempi

Nel modello di esempio seguente vengono illustrate diverse modalità di aggiunta di valori temporali.

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

Quando il modello precedente viene distribuito con un'ora di `2020-04-07 14:53:14Z`base, l'output è:

| Nome | Type | valore |
| ---- | ---- | ----- |
| add3Years | string | 4/7/2023 2:53:14 PM |
| subtract9Days | string | 3/29/2020 2:53:14 PM |
| add1Hour | string | 4/7/2020 3:53:14 PM |

Il modello di esempio successivo Mostra come impostare l'ora di inizio per una pianificazione di automazione.

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

Restituisce il valore DateTime corrente (UTC) nel formato specificato. Se non viene specificato alcun formato, viene usato il formato ISO 8601 (yyyyMMddTHHmmssZ). **Questa funzione può essere usata solo nel valore predefinito per un parametro.**

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| format |No |stringa |Valore URI codificato da convertire in stringa. Utilizzare [stringhe di formato standard](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [stringhe di formato personalizzate](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Osservazioni

Questa funzione può essere usata solo all'interno di un'espressione per il valore predefinito di un parametro. L'uso di questa funzione in qualsiasi altra posizione in un modello restituisce un errore. La funzione non è consentita in altre parti del modello perché restituisce un valore diverso ogni volta che viene chiamato. La distribuzione dello stesso modello con gli stessi parametri non produrrebbe in modo affidabile gli stessi risultati.

Se si usa l' [opzione per ridistribuire una distribuzione riuscita in precedenza](rollback-on-error.md)e la distribuzione precedente include un parametro che usa UtcNow, il parametro non viene rivalutato. Al contrario, il valore del parametro della distribuzione precedente viene riutilizzato automaticamente nella distribuzione di rollback.

Prestare attenzione nella ridistribuzione di un modello che si basa sulla funzione utcNow per un valore predefinito. Quando si esegue la ridistribuzione e non si fornisce un valore per il parametro, la funzione viene rivalutata. Se si desidera aggiornare una risorsa esistente anziché crearne una nuova, passare il valore del parametro dalla distribuzione precedente.

### <a name="return-value"></a>Valore restituito

Valore DateTime UTC corrente.

### <a name="examples"></a>Esempi

Il modello di esempio seguente mostra formati diversi per il valore DateTime.

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

L'output dell'esempio precedente varia per ogni distribuzione, ma sarà simile a:

| Nome | Type | valore |
| ---- | ---- | ----- |
| utcOutput | stringa | 20190305T175318Z |
| utcShortOutput | stringa | 05/03/2019 |
| utcCustomOutput | stringa | 3 5 |

Nell'esempio seguente viene illustrato come utilizzare un valore della funzione quando si imposta un valore di tag.

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

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
