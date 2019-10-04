---
title: Parametri nei modelli di Azure Resource Manager
description: Viene descritto come definire i parametri in un modello di Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4947b00d6fad5007751cd97d43ad6aca8d775330
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383280"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parametri nei modelli di Azure Resource Manager

Questo articolo descrive come definire e usare i parametri nel modello di Azure Resource Manager. Fornendo valori diversi per i parametri, è possibile riutilizzare un modello per ambienti diversi.

Gestione risorse risolve i valori dei parametri prima di avviare le operazioni di distribuzione. Quando il parametro viene usato nel modello, Gestione risorse lo sostituisce con il valore risolto.

## <a name="define-parameter"></a>Definisci parametro

L'esempio seguente illustra la definizione di un parametro semplice. Definisce un parametro denominato **storageSKU**. Il parametro è un valore stringa e accetta solo valori validi per l'utilizzo previsto. Il parametro usa un valore predefinito quando non viene specificato alcun valore durante la distribuzione.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Usa parametro

Nel modello si fa riferimento al valore per il parametro usando la funzione [Parameters](resource-group-template-functions-deployment.md#parameters) . Nell'esempio seguente il valore del parametro viene usato per impostare lo SKU per l'account di archiviazione.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Funzioni di modello

Quando si specifica il valore predefinito per un parametro, è possibile usare la maggior parte delle funzioni del modello. Si può usare il valore di un altro parametro per generare un valore predefinito. Il modello seguente illustra l'uso delle funzioni nel valore predefinito. Quando non viene fornito alcun nome per il sito, viene creato un valore stringa univoco che viene aggiunto al **sito**. Quando non viene fornito alcun nome per il piano host, accetta il valore del sito e aggiunge **-Plan**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Non è possibile usare la funzione [Reference](resource-group-template-functions-resource.md#reference) o una delle funzioni [elenco](resource-group-template-functions-resource.md#list) nella sezione Parameters. Queste funzioni ottengono lo stato di runtime di una risorsa e non possono essere eseguite prima della distribuzione quando i parametri vengono risolti.

## <a name="objects-as-parameters"></a>Oggetti come parametri

Può essere più semplice organizzare i valori correlati passandoli come oggetto. Questo approccio riduce anche il numero di parametri nel modello.

Nell'esempio seguente viene illustrato un parametro che è un oggetto. Il valore predefinito Mostra le proprietà previste per l'oggetto.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Si fa riferimento alle proprietà dell'oggetto usando l'operatore punto.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Modelli di esempio

Negli esempi seguenti vengono illustrati scenari per l'utilizzo di parametri.

|Modello  |Descrizione  |
|---------|---------|
|[parametri con funzioni per i valori predefiniti](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Illustra come usare le funzioni di modello quando si definiscono valori predefiniti per i parametri. Il modello non distribuisce alcuna risorsa. Crea valori di parametro e restituisce questi valori. |
|[oggetto parametro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Illustra l'uso di un oggetto per un parametro. Il modello non distribuisce alcuna risorsa. Crea valori di parametro e restituisce questi valori. |


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per i parametri, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per informazioni sul passaggio dei valori dei parametri come file, vedere [creare Gestione risorse file di parametri](resource-manager-parameter-files.md).
* Per consigli sulla creazione di parametri, vedere procedure consigliate [-parametri](template-best-practices.md#parameters).
