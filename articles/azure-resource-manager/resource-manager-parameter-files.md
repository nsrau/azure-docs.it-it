---
title: Creare il file di parametri
description: Creare un file di parametri per passare i valori durante la distribuzione di un modello di Azure Resource Manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 7333e33af90ff7883b53a24bacdc63b42bf107ee
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149460"
---
# <a name="create-resource-manager-parameter-file"></a>Crea Gestione risorse file di parametri

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. Questo articolo illustra come creare il file di parametri.

## <a name="parameter-file"></a>File di parametri

Il file dei parametri ha il formato seguente:

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

Si noti che i valori dei parametri vengono archiviati come testo normale nel file dei parametri. Questo approccio funziona per i valori che non sono sensibili, ad esempio per specificare lo SKU per una risorsa. Non funziona per i valori sensibili, ad esempio le password. Se è necessario passare un valore sensibile come parametro, archiviare il valore in un insieme di credenziali delle chiavi e fare riferimento all'insieme di credenziali delle chiavi nel file dei parametri. Il valore sensibile viene recuperato in modo sicuro durante la distribuzione.

Il file di parametri seguente include un valore di testo normale e un valore archiviato in un insieme di credenziali delle chiavi.

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

Per altre informazioni sull'uso di valori da un insieme di credenziali delle chiavi, vedere [usare Azure Key Vault per passare il valore di un parametro sicuro durante la distribuzione](resource-manager-keyvault-parameter.md).

## <a name="define-parameter-values"></a>Definire i valori dei parametri

Per capire come definire i valori dei parametri, aprire il modello che si sta distribuendo. Esaminare la sezione Parameters del modello. Nell'esempio seguente vengono illustrati i parametri di un modello.

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

Il primo dettaglio da notare è il nome di ogni parametro. I valori nel file di parametri devono corrispondere ai nomi.

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

Si noti il tipo di parametro. I valori nel file di parametri devono avere gli stessi tipi. Per questo modello, è possibile specificare entrambi i parametri come stringhe.

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

Cercare quindi un valore predefinito. Se un parametro ha un valore predefinito, è possibile specificare un valore, ma non è necessario.

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

Infine, esaminare i valori consentiti e qualsiasi restrizione come Max Length. Indicano l'intervallo di valori che è possibile fornire per il parametro.

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

## <a name="parameter-type-formats"></a>Formati di tipi di parametro

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

La convenzione generale per la denominazione del file dei parametri consiste nell'aggiungere **. Parameters** al nome del modello. Ad esempio, se il modello è denominato **file azuredeploy. JSON**, il file dei parametri è denominato **file azuredeploy. Parameters. JSON**. Questa convenzione di denominazione consente di visualizzare la connessione tra il modello e i parametri.

Per eseguire la distribuzione in ambienti diversi, creare più di un file di parametri. Quando si denomina il file di parametri, aggiungere una modalità per identificarne l'uso. Ad esempio, usare **file azuredeploy. Parameters-dev. JSON** e **file azuredeploy. Parameters-prod. JSON**


## <a name="parameter-precedence"></a>Precedenza dei parametri

È possibile usare i parametri inline e un file di parametri locale nella stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori inline durante la distribuzione. Se si specificano valori per un parametro sia nel file dei parametri locale che inline, il valore inline ha la precedenza.

Tuttavia, quando si usa un file di parametri esterni, non è possibile trasmettere altri valori, né inline né da un file locale. Tutti i parametri inline vengono ignorati. È necessario fornire tutti i valori dei parametri presenti nel file esterno.

## <a name="parameter-name-conflicts"></a>Conflitti nei nomi di parametro

Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando di PowerShell, PowerShell aggiunge al parametro del modello il suffisso **FromTemplate**. Ad esempio, un parametro denominato **ResourceGroupName** nel modello sarà in conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Verrà quindi richiesto di fornire un valore per **ResourceGroupNameFromTemplate**. È possibile evitare questa confusione usando nomi di parametro non usati per i comandi di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come definire i parametri nel modello, vedere [Parameters in Azure Resource Manager Templates](template-parameters.md).
- Per altre informazioni sull'uso di valori da un insieme di credenziali delle chiavi, vedere [usare Azure Key Vault per passare il valore di un parametro sicuro durante la distribuzione](resource-manager-keyvault-parameter.md).
- Per ulteriori informazioni sui parametri, vedere [parametri in Azure Resource Manager modelli](template-parameters.md).
