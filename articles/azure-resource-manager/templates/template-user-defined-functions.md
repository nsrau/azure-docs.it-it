---
title: Funzioni definite dall'utente nei modelli
description: Viene descritto come definire e utilizzare funzioni definite dall'utente in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: e2e68c6f6b9fc9eb40d282d87572ce1e805a11a8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483804"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funzioni definite dall'utente nel modello di Azure Resource Manager

Nel modello è possibile creare funzioni personalizzate. Tali funzioni sono disponibili per usare il modello. Le funzioni definite dall'utente sono separate dalle [funzioni di modello standard](template-functions.md) che sono automaticamente disponibili all'interno del modello. Creare funzioni personalizzate quando si hanno espressioni complesse che vengono usate più volte nel modello.

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
* La funzione può usare solo i parametri definiti in essa. Quando si usa la funzione [Parameters](template-functions-deployment.md#parameters) in una funzione definita dall'utente, si è limitati ai parametri della funzione.
* La funzione non può chiamare altre funzioni definite dall'utente.
* La funzione non può utilizzare la funzione [Reference](template-functions-resource.md#reference) o una delle funzioni [elenco](template-functions-resource.md#list) .
* I parametri della funzione non possono avere valori predefiniti.


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le funzioni definite dall'utente, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
* Per un elenco delle funzioni di modello disponibili, vedere [Azure Resource Manager funzioni di modello](template-functions.md).
