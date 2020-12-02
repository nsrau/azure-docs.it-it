---
title: Funzioni definite dall'utente nei modelli
description: Viene descritto come definire e usare funzioni definite dall'utente in un modello di Azure Resource Manager (modello ARM).
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 12199aca788438e4558ed2c607b866f0dc723e2a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436964"
---
# <a name="user-defined-functions-in-arm-template"></a>Funzioni definite dall'utente nel modello ARM

Nel modello è possibile creare funzioni personalizzate. Tali funzioni sono disponibili per usare il modello. Le funzioni definite dall'utente sono separate dalle [funzioni di modello standard](template-functions.md) che sono automaticamente disponibili all'interno del modello. Creare funzioni personalizzate quando si hanno espressioni complesse che vengono usate più volte nel modello.

Questo articolo descrive come aggiungere funzioni definite dall'utente nel modello di Azure Resource Manager (modello ARM).

## <a name="define-the-function"></a>Definire la funzione

Le funzioni richiedono che sia definito un valore dello spazio dei nomi per evitare conflitti di denominazione con le funzioni del modello. Nell'esempio seguente viene illustrata una funzione che restituisce un nome univoco:

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

Nell'esempio seguente viene illustrato un modello che include una funzione definita dall'utente. USA tale funzione per ottenere un nome univoco per un account di archiviazione. Il modello contiene un parametro denominato **storageNamePrefix** che viene passato come parametro alla funzione.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
* La funzione può usare solo i parametri definiti in essa. Quando si usa la funzione [Parameters](template-functions-deployment.md#parameters) in una funzione definita dall'utente, si è limitati ai parametri della funzione.
* La funzione non può chiamare altre funzioni definite dall'utente.
* La funzione non può utilizzare la funzione [Reference](template-functions-resource.md#reference) o una delle funzioni [elenco](template-functions-resource.md#list) .
* I parametri della funzione non possono avere valori predefiniti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le funzioni definite dall'utente, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per un elenco delle funzioni di modello disponibili, vedere [funzioni modello ARM](template-functions.md).
