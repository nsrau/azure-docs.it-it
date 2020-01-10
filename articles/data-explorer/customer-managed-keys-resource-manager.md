---
title: Configurare chiavi gestite dal cliente in Azure Esplora dati usando il modello di Azure Resource Manager
description: Questo articolo descrive come configurare la crittografia delle chiavi gestite dal cliente nei dati in Azure Esplora dati usando il modello di Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: bff8c50cdece803e030c0975a9b14ac5739baaf7
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725806"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Configurare chiavi gestite dal cliente usando il modello di Azure Resource Manager

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Modello di Azure Resource Manager](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

In questa sezione vengono configurate le chiavi gestite dal cliente usando modelli Azure Resource Manager. Per impostazione predefinita, la crittografia Esplora dati di Azure usa chiavi gestite da Microsoft. In questo passaggio, configurare il cluster di Azure Esplora dati per l'uso delle chiavi gestite dal cliente e specificare la chiave da associare al cluster.

È possibile distribuire il modello di Azure Resource Manager usando il portale di Azure o tramite PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
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
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare il cluster per usare la nuova versione. Per prima cosa, chiamare `Get-AzKeyVaultKey` per ottenere la versione più recente della chiave. Aggiornare quindi le proprietà dell'insieme di credenziali delle chiavi del cluster per usare la nuova versione della chiave, come illustrato nella pagina relativa alla [configurazione della crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i cluster di Azure Esplora dati in Azure](security.md)
* [Configurare le identità gestite per il cluster di Azure Esplora dati](managed-identities.md)
* [Proteggere il cluster in Azure Esplora dati-portale di Azure](manage-cluster-security.md) abilitando la crittografia dei computer inattivi.
* [Configurare chiavi gestite dal cliente medianteC#](customer-managed-keys-csharp.md)

