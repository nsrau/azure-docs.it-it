---
title: Funzioni modello-data
description: Descrive le funzioni da usare in un modello di Azure Resource Manager per lavorare con le date.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 83e601adb649098f7a4e19cb71170b96a3287d9b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96004586"
---
# <a name="date-functions-for-arm-templates"></a>Funzioni di data per i modelli ARM

Gestione risorse fornisce le funzioni seguenti per l'utilizzo delle date nei modelli di Azure Resource Manager (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Aggiunge una durata di tempo a un valore di base. È previsto il formato ISO 8601.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| base | Sì | string | Valore DateTime iniziale per l'addizione. Usare il [formato timestamp ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sì | string | Valore di ora da aggiungere alla base. Può essere un valore negativo. Usare il [formato di durata ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | No | string | Formato di output per il risultato della data e ora. Se non viene specificato, viene usato il formato del valore di base. Utilizzare [stringhe di formato standard](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [stringhe di formato personalizzate](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valore restituito

Valore DateTime risultante dall'aggiunta del valore Duration al valore di base.

### <a name="examples"></a>Esempio

Nel modello di esempio seguente vengono illustrate diverse modalità di aggiunta di valori temporali.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "baseTime": {
      "type": "string",
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
    "add3YearsOutput": {
      "value": "[variables('add3Years')]",
      "type": "string"
    },
    "subtract9DaysOutput": {
      "value": "[variables('subtract9Days')]",
      "type": "string"
    },
    "add1HourOutput": {
      "value": "[variables('add1Hour')]",
      "type": "string"
    },
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

---

Quando il modello precedente viene distribuito con un'ora di base `2020-04-07 14:53:14Z` , l'output è:

| Nome | Type | valore |
| ---- | ---- | ----- |
| add3YearsOutput | string | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | string | 3/29/2020 2:53:14 PM |
| add1HourOutput | string | 4/7/2020 3:53:14 PM |

Il modello di esempio successivo Mostra come impostare l'ora di inizio per una pianificazione di automazione.

# <a name="json"></a>[JSON](#tab/json)

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
    "baseTime": {
      "type": "string",
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
    ...
    {
      "type": "Microsoft.Automation/automationAccounts/schedules",
      "apiVersion": "2015-10-31",
      "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",

      "properties": {
        "description": "Demo Scheduler",
        "startTime": "[variables('startTime')]",
        "interval": 1,
        "frequency": "Hour"
      }
    }
  ],
  "outputs": {
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

---

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Restituisce il valore DateTime corrente (UTC) nel formato specificato. Se non viene specificato alcun formato, viene usato il formato ISO 8601 (yyyyMMddTHHmmssZ). **Questa funzione può essere usata solo nel valore predefinito per un parametro.**

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| format |No |string |Valore URI codificato da convertire in stringa. Utilizzare [stringhe di formato standard](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [stringhe di formato personalizzate](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Commenti

Questa funzione può essere usata solo all'interno di un'espressione per il valore predefinito di un parametro. L'uso di questa funzione in qualsiasi altra posizione in un modello restituisce un errore. La funzione non è consentita in altre parti del modello perché restituisce un valore diverso ogni volta che viene chiamato. La distribuzione dello stesso modello con gli stessi parametri non produrrebbe in modo affidabile gli stessi risultati.

Se si usa l' [opzione per eseguire il rollback in](rollback-on-error.md) caso di errore in una distribuzione precedente riuscita e la distribuzione precedente include un parametro che usa UtcNow, il parametro non viene rivalutato. Al contrario, il valore del parametro della distribuzione precedente viene riutilizzato automaticamente nella distribuzione di rollback.

Prestare attenzione nella ridistribuzione di un modello che si basa sulla funzione utcNow per un valore predefinito. Quando si esegue la ridistribuzione e non si fornisce un valore per il parametro, la funzione viene rivalutata. Se si desidera aggiornare una risorsa esistente anziché crearne una nuova, passare il valore del parametro dalla distribuzione precedente.

### <a name="return-value"></a>Valore restituito

Valore DateTime UTC corrente.

### <a name="examples"></a>Esempio

Il modello di esempio seguente mostra formati diversi per il valore DateTime.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

---

L'output dell'esempio precedente varia per ogni distribuzione, ma sarà simile a:

| Nome | Type | valore |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 05/03/2019 |
| utcCustomOutput | string | 3 5 |

Nell'esempio seguente viene illustrato come utilizzare un valore della funzione quando si imposta un valore di tag.

# <a name="json"></a>[JSON](#tab/json)

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
      "tags": {
        "createdDate": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ],
  "outputs": {
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShort')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2018-05-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
