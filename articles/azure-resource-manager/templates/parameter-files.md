---
title: Creare il file di parametri
description: Creare un file di parametri per il passaggio di valori durante la distribuzione di un modello di Azure Resource ManagerCreate parameter file for passing in values during deployment of an Azure Resource Manager template
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: bb52d9c5ebcb0820362e5de3d6b24b0b18d742e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155622"
---
# <a name="create-resource-manager-parameter-file"></a>Creare un file di parametri di Resource ManagerCreate Resource Manager parameter file

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. In questo articolo viene illustrato come creare il file dei parametri.

## <a name="parameter-file"></a>File di parametri

Il file dei parametri ha il seguente formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Si noti che i valori dei parametri vengono archiviati come testo normale nel file dei parametri. Questo approccio funziona per i valori che non sono sensibili, ad esempio specificando lo SKU per una risorsa. Non funziona per i valori sensibili, ad esempio le password. Se è necessario passare un valore sensibile come parametro, archiviare il valore in un insieme di credenziali delle chiavi e fare riferimento all'insieme di credenziali delle chiavi nel file dei parametri. Il valore sensibile viene recuperato in modo sicuro durante la distribuzione.

Il seguente file di parametri include un valore di testo normale e un valore archiviato in un insieme di credenziali delle chiavi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Per altre informazioni sull'uso dei valori da un insieme di credenziali delle chiavi, vedere Usare l'insieme di credenziali delle chiavi di Azure per passare il valore dei [parametri protetti durante la distribuzione.](key-vault-parameter.md)

## <a name="define-parameter-values"></a>Definire i valori dei parametri

Per capire come definire i valori dei parametri, aprire il modello che si sta distribuendo. Esaminare la sezione dei parametri del modello. Nell'esempio seguente vengono illustrati i parametri di un modello.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

Il primo dettaglio da notare è il nome di ogni parametro. I valori nel file dei parametri devono corrispondere ai nomi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Si noti il tipo del parametro. I valori nel file dei parametri devono avere gli stessi tipi. Per questo modello, è possibile fornire entrambi i parametri come stringhe.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Successivamente, cercare un valore predefinito. Se un parametro ha un valore predefinito, è possibile fornire un valore ma non è necessario.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Infine, esaminare i valori consentiti e le eventuali restrizioni come lunghezza massima. Indicano l'intervallo di valori che è possibile fornire per il parametro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

## <a name="parameter-type-formats"></a>Formati dei tipi di parametro

Nell'esempio seguente vengono illustrati i formati dei diversi tipi di parametro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="file-name"></a>Nome file

La convenzione generale per la denominazione del file dei parametri consiste nell'aggiungere **.parameters** al nome del modello. Ad esempio, se il modello è denominato **azuredeploy.json**, il file dei parametri è denominato **azuredeploy.parameters.json**. Questa convenzione di denominazione consente di visualizzare la connessione tra il modello e i parametri.

Per eseguire la distribuzione in ambienti diversi, creare più di un file di parametri. Quando si assegna un nome al file dei parametri, aggiungere un modo per identificarne l'utilizzo. Ad esempio, usare **azuredeploy.parameters-dev.json** e **azuredeploy.parameters-prod.jsonFor** example, use azuredeploy.parameters-dev and azuredeploy.parameters-prod.json


## <a name="parameter-precedence"></a>Precedenza dei parametri

È possibile usare i parametri inline e un file di parametri locale nella stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori inline durante la distribuzione. Se si specificano valori per un parametro sia nel file dei parametri locale che inline, il valore inline ha la precedenza.

Tuttavia, quando si usa un file di parametri esterni, non è possibile trasmettere altri valori, né inline né da un file locale. Tutti i parametri inline vengono ignorati. È necessario fornire tutti i valori dei parametri presenti nel file esterno.

## <a name="parameter-name-conflicts"></a>Conflitti nei nomi di parametro

Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando di PowerShell, PowerShell aggiunge al parametro del modello il suffisso **FromTemplate**. Ad esempio, un parametro denominato **ResourceGroupName** nel modello sarà in conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Verrà quindi richiesto di fornire un valore per **ResourceGroupNameFromTemplate**. È possibile evitare questa confusione utilizzando i nomi dei parametri che non vengono utilizzati per i comandi di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come definire i parametri nel modello, vedere Parametri nei modelli di [Azure Resource Manager.](template-parameters.md)
- Per altre informazioni sull'uso dei valori da un insieme di credenziali delle chiavi, vedere Usare l'insieme di credenziali delle chiavi di Azure per passare il valore dei [parametri protetti durante la distribuzione.](key-vault-parameter.md)
- Per altre informazioni sui parametri, vedere Parametri nei modelli di Azure Resource Manager.For more information about parameters, see [Parameters in Azure Resource Manager templates.](template-parameters.md)
