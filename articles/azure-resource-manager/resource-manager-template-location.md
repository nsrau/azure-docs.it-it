---
title: Posizione delle risorse di Azure nei modelli | Microsoft Docs
description: Viene illustrato come impostare una posizione per una risorsa in un modello di Azure Resource Manager
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Impostare la posizione delle risorse nei modelli di Azure Resource Manager
Quando si distribuisce un modello, è necessario fornire la posizione per ogni risorsa. In questo argomento viene illustrato come determinare le posizioni disponibili per la sottoscrizione per ogni tipo di risorsa.

## <a name="determine-supported-locations"></a>Determinare le posizioni supportate

Per un elenco completo delle posizioni supportate per ciascun tipo di risorsa, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/). Tuttavia, la sottoscrizione potrebbe non avere accesso a tutte le posizioni indicate in tale elenco. Per visualizzare un elenco personalizzato delle posizioni disponibili per la sottoscrizione, usare Azure PowerShell o l'interfaccia della riga di comando di Azure. 

Nell'esempio seguente viene usato PowerShell per ottenere la posizione per il tipo di risorsa `Microsoft.Web\sites`:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Nell'esempio seguente viene usata l'interfaccia della riga di comando di Azure 2.0 per ottenere la posizione per il tipo di risorsa `Microsoft.Web\sites`:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>Impostare la posizione nel modello

Dopo aver determinato le posizioni supportate per le risorse, è necessario impostare la posizione del modello. Il modo più semplice per impostare questo valore consiste nel creare un gruppo di risorse in una posizione che supporti i tipi di risorsa e impostare ogni posizione su `[resourceGroup().location]`. È possibile ridistribuire il modello a gruppi di risorse in posizioni diverse senza modificare i valori o i parametri del modello. 

Nell'esempio seguente viene illustrato un account di archiviazione che viene distribuito nella stessa posizione del gruppo di risorse:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Se è necessario impostare come hardcoded la posizione del modello, specificare il nome di una delle aree supportate. Nell'esempio seguente viene illustrato un account di archiviazione che viene sempre distribuito negli Stati Uniti centro-settentrionali:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per altri suggerimenti su come creare i modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](resource-manager-template-best-practices.md).

