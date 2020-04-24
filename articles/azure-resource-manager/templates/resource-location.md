---
title: Percorso risorsa modello
description: Viene descritto come impostare la posizione delle risorse in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156430"
---
# <a name="set-resource-location-in-arm-template"></a>Impostare la posizione delle risorse nel modello ARM

Quando si distribuisce un modello di Azure Resource Manager (ARM), è necessario specificare un percorso per ogni risorsa. Il percorso non deve necessariamente corrispondere alla località del gruppo di risorse.

## <a name="get-available-locations"></a>Ottenere le località disponibili

Diversi tipi di risorse sono supportati in posizioni diverse. Per ottenere i percorsi supportati per un tipo di risorsa, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Usa parametro percorso

Per garantire flessibilità durante la distribuzione del modello, usare un parametro per specificare il percorso delle risorse. Impostare il valore predefinito del parametro su `resourceGroup().location`.

L'esempio seguente illustra un account di archiviazione che viene distribuito in una posizione specificata come parametro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](template-functions.md).
* Per altre informazioni sui file modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
