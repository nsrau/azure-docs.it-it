---
title: Funzioni definite dall'utente nei modelliUser-defined functions in templates
description: Viene descritto come definire e usare funzioni definite dall'utente in un modello di Azure Resource Manager.Describes how to define and use user-defined functions in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943209"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funzioni definite dall'utente nel modello di Azure Resource ManagerUser-defined functions in Azure Resource Manager template

Nel modello è possibile creare funzioni personalizzate. Tali funzioni sono disponibili per usare il modello. Le funzioni definite dall'utente sono separate dalle [funzioni del modello standard](template-functions.md) che sono automaticamente disponibili all'interno del modello. Creare funzioni personalizzate quando si hanno espressioni complesse che vengono utilizzate ripetutamente nel modello.

Questo articolo descrive come aggiungere funzioni definite dall'utente nel modello di Azure Resource Manager.This article describes how to add user-defined functions in your Azure Resource Manager template.

## <a name="define-the-function"></a>Definire la funzione

Le funzioni richiedono che sia definito un valore dello spazio dei nomi per evitare conflitti di denominazione con le funzioni del modello. Nell'esempio seguente viene illustrata una funzione che restituisce un nome univoco:The following example shows a function that returns a unique name:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Utilizzare la funzione

Nell'esempio seguente viene illustrato un modello che include una funzione definita dall'utente. Usa tale funzione per ottenere un nome univoco per un account di archiviazione. Il modello ha un parametro denominato **storageNamePrefix** che passa come parametro alla funzione.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Limitazioni

Quando si crea una funzione definita dall'utente, è necessario tenere presente alcune restrizioni:

* La funzione non può accedere alle variabili.
* La funzione può usare solo i parametri definiti in essa. Quando si utilizza la funzione [parameters](template-functions-deployment.md#parameters) all'interno di una funzione definita dall'utente, si è limitati ai parametri per tale funzione.
* La funzione non può chiamare altre funzioni definite dall'utente.
* La funzione non può utilizzare la funzione [di riferimento](template-functions-resource.md#reference) o una delle funzioni [di elenco.](template-functions-resource.md#list)
* I parametri della funzione non possono avere valori predefiniti.


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le funzioni definite dall'utente, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](template-syntax.md)
* Per un elenco delle funzioni di modello disponibili, vedere Funzioni del modello di [Azure Resource Manager](template-functions.md).
