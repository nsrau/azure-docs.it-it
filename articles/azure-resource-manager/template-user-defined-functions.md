---
title: Funzioni definite dall'utente nel modello di Azure Resource Manager
description: Viene descritto come definire e utilizzare funzioni definite dall'utente in un modello di Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 71c166b9c596c3c8628f943ae5c7dbebd9c2d51c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384138"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funzioni definite dall'utente nel modello di Azure Resource Manager

Nel modello è possibile creare funzioni personalizzate. Tali funzioni sono disponibili per usare il modello. Le funzioni definite dall'utente sono separate dalle [funzioni di modello standard](resource-group-template-functions.md) che sono automaticamente disponibili all'interno del modello. Creare funzioni personalizzate quando si hanno espressioni complesse che vengono usate più volte nel modello.

Questo articolo descrive come aggiungere funzioni definite dall'utente nel modello di Azure Resource Manager.

## <a name="define-the-function"></a>Definire la funzione

Le funzioni richiedono che sia definito un valore dello spazio dei nomi per evitare conflitti di denominazione con le funzioni del modello. L'esempio illustrata una funzione che restituisce un nome di account di archiviazione:

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

## <a name="use-the-function"></a>Usare la funzione

Nell'esempio seguente viene illustrato come chiamare la funzione.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Limitazioni

Quando si crea una funzione definita dall'utente, è necessario tenere presente alcune restrizioni:

* La funzione non può accedere alle variabili.
* La funzione può usare solo i parametri definiti in essa. Quando si usa la funzione [Parameters](resource-group-template-functions-deployment.md#parameters) in una funzione definita dall'utente, si è limitati ai parametri della funzione.
* La funzione non può chiamare altre funzioni definite dall'utente.
* La funzione non può utilizzare le funzioni di [riferimento](resource-group-template-functions-resource.md#reference) o di [elenco](resource-group-template-functions-resource.md#list) .
* I parametri della funzione non possono avere valori predefiniti.


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le funzioni definite dall'utente, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per un elenco delle funzioni di modello disponibili, vedere [Azure Resource Manager funzioni di modello](resource-group-template-functions.md).
