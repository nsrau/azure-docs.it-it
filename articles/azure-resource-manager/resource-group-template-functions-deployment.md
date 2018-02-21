---
title: Funzioni di distribuzione del modello di Azure Resource Manager | Microsoft Docs
description: "Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per recuperare informazioni relative alla distribuzione."
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
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 17fe2bc467acc5542d021961a066940dbecf6120
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funzioni di distribuzione per i modelli di Azure Resource Manager 

Gestione risorse fornisce le funzioni seguenti per ottenere i valori dalle sezioni del modello e i valori relativi alla distribuzione:

* [deployment](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Per ottenere valori da risorse, gruppi di risorse o sottoscrizioni, vedere [Funzioni delle risorse](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>distribuzione
`deployment()`

Restituisce informazioni sull'operazione di distribuzione corrente.

### <a name="return-value"></a>Valore restituito

Questa funzione restituisce l'oggetto che viene passato durante la distribuzione. Le proprietà nell'oggetto restituito variano a seconda che l'oggetto di distribuzione venga passato come un collegamento o come un oggetto inline. Quando l'oggetto di distribuzione viene passato inline, ad esempio quando si usa il parametro **-TemplateFile** in Azure PowerShell per puntare a un file locale, l'oggetto restituito è nel formato seguente:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando l'oggetto viene passato come collegamento, come quando si usa il parametro **-TemplateUri** per puntare a un oggetto remoto, l'oggetto viene restituito nel formato seguente: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Osservazioni

È possibile usare deployment() per il collegamento a un altro modello in base all'URI del modello padre.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) seguente restituisce l'oggetto di distribuzione:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

L'esempio precedente restituisce l'oggetto seguente:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

<a id="parameters" />

## <a name="parameters"></a>Parametri
`parameters(parameterName)`

Restituisce un valore di parametro. Il nome del parametro specificato deve essere definito nella sezione parameters del modello.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| parameterName |Sì |stringa |Nome del parametro da restituire. |

### <a name="return-value"></a>Valore restituito

Il valore del parametro specificato.

### <a name="remarks"></a>Osservazioni

Per impostare i valori delle risorse, si usano in genere i parametri. Nell'esempio seguente il nome del sito Web viene impostato sul valore del parametro passato durante la distribuzione.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) seguente mostra un uso semplificato della funzione parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| stringOutput | string | option 1 |
| intOutput | int | 1 |
| objectOutput | Oggetto | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | string | option 1 |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

Restituisce il valore della variabile. Il nome della variabile specificato deve essere definito nella sezione variables del modello.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| variableName |Sì |string |Nome della variabile da restituire. |

### <a name="return-value"></a>Valore restituito

Il valore della variabile specificata.

### <a name="remarks"></a>Osservazioni

Per semplificare il modello creando valori complessi una sola volta, si usano in genere le variabili. Nell'esempio seguente viene creato un nome univoco per un account di archiviazione.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) seguente restituisce valori di variabile diversi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | type | Valore |
| ---- | ---- | ----- |
| exampleOutput1 | string | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | string | myVariable |
| exampleOutput4 |  Oggetto | {"property1": "value1", "property2": "value2"} |

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).

