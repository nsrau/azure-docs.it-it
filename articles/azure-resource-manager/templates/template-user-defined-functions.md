---
title: Funzioni definite dall'utente nei modelli
description: Viene descritto come definire e utilizzare funzioni definite dall'utente in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 58b9ba7b162736329cf775e2be5a47bfcae0a4ca
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122475"
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
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "location": "South Central US",
    "tags": {},
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
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
