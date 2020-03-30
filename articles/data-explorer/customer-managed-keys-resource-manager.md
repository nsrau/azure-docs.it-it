---
title: Configurare le chiavi gestite dal cliente in Azure Data Explorer usando il modello di Azure Resource ManagerConfigure customer-managed-keys in Azure Data Explorer using the Azure Resource Manager template
description: Questo articolo descrive come configurare la crittografia delle chiavi gestite dal cliente nei dati in Azure Data Explorer usando il modello di Azure Resource Manager.This article describes how to configure customer-managed keys encryption on your data in Azure Data Explorer using the Azure Resource Manager template.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297874"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Configurare le chiavi gestite dal cliente usando il modello di Azure Resource ManagerConfigure customer-managed-keys using the Azure Resource Manager template

> [!div class="op_single_selector"]
> * [Portale](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal clienteConfigure encryption with customer-managed keys

In questa sezione vengono configurate le chiavi gestite dal cliente usando i modelli di Azure Resource Manager.In this section, you configure customer-managed keys using Azure Resource Manager templates. Per impostazione predefinita, la crittografia di Azure Data Explorer usa chiavi gestite da Microsoft.By default, Azure Data Explorer encryption uses Microsoft-managed keys. In questo passaggio configurare il cluster di Azure Data Explorer per l'uso delle chiavi gestite dal cliente e specificare la chiave da associare al cluster.

È possibile distribuire il modello di Azure Resource Manager usando il portale di Azure o PowerShell.You can deploy the Azure Resource Manager template by using the Azure portal or using PowerShell.

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

Quando si crea una nuova versione di una chiave, è necessario aggiornare il cluster per utilizzare la nuova versione. In primo `Get-AzKeyVaultKey` luogo, chiamare per ottenere la versione più recente della chiave. Aggiornare quindi le proprietà dell'insieme di credenziali delle chiavi del cluster per utilizzare la nuova versione della chiave, come illustrato in Configurare la [crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i cluster di Azure Data Explorer in AzureSecure Azure Data Explorer clusters in Azure](security.md)
* [Configurare le identità gestite per il cluster di Azure Data ExplorerConfigure managed identities for your Azure Data Explorer cluster](managed-identities.md)
* [Proteggere il cluster in Azure Data Explorer - Portale](manage-cluster-security.md) di Azure abilitando la crittografia inattivi.
* [Configurare le chiavi gestite dal cliente usando CConfigure customer-managed-keys using C #](customer-managed-keys-csharp.md)

