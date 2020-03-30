---
title: Parametri nei modelli
description: Viene descritto come definire i parametri in un modello di Azure Resource Manager.Describes how to define parameters in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122424"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parametri nei modelli di Azure Resource ManagerParameters in Azure Resource Manager templates

Questo articolo descrive come definire e usare i parametri nel modello di Azure Resource Manager.This article describes how to define and use parameters in your Azure Resource Manager template. Fornendo valori diversi per i parametri, è possibile riutilizzare un modello per ambienti diversi.

Resource Manager risolve i valori dei parametri prima di avviare le operazioni di distribuzione. Ovunque il parametro venga utilizzato nel modello, Resource Manager lo sostituisce con il valore risolto.

## <a name="define-parameter"></a>Definisci parametro

L'esempio seguente illustra la definizione di un parametro semplice. Definisce un parametro denominato **storageSKU**. Il parametro è un valore stringa e accetta solo valori validi per l'utilizzo previsto. Il parametro utilizza un valore predefinito quando non viene fornito alcun valore durante la distribuzione.

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

Nel modello, si fa riferimento al valore per il parametro utilizzando la funzione [parameters](template-functions-deployment.md#parameters) . Nell'esempio seguente il valore del parametro viene usato per impostare lo SKU per l'account di archiviazione.

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

Quando si specifica il valore predefinito per un parametro, è possibile usare la maggior parte delle funzioni del modello. Si può usare il valore di un altro parametro per generare un valore predefinito. Nel modello seguente viene illustrato l'utilizzo delle funzioni nel valore predefinito. Quando non viene fornito alcun nome per il sito, crea un valore stringa univoco e lo aggiunge al **sito**. Quando non viene fornito alcun nome per il piano host, accetta il valore per il sito e aggiunge **-plan**.

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

Non è possibile utilizzare la funzione [di riferimento](template-functions-resource.md#reference) o una delle funzioni [di elenco](template-functions-resource.md#list) nella sezione parameters. Queste funzioni ottengono lo stato di runtime di una risorsa e non possono essere eseguite prima della distribuzione quando i parametri vengono risolti.

## <a name="objects-as-parameters"></a>Oggetti come parametri

Può essere più semplice organizzare i valori correlati passandoli come oggetto. Questo approccio riduce anche il numero di parametri nel modello.

Nell'esempio seguente viene illustrato un parametro che è un oggetto. Il valore predefinito mostra le proprietà previste per l'oggetto.

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

Fare riferimento alle proprietà dell'oggetto utilizzando l'operatore punto.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
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

* Per informazioni sulle proprietà disponibili per i parametri, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](template-syntax.md)
* Per informazioni sul passaggio dei valori dei parametri come file, vedere Creare un file di parametri di [Resource Manager.](parameter-files.md)
* Per suggerimenti sulla creazione di parametri, vedere [Procedure consigliate - parametri](template-best-practices.md#parameters).
