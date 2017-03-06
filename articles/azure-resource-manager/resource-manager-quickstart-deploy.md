---
title: Distribuire risorse in Azure | Microsoft Docs
description: Usare Azure PowerShell o l&quot;interfaccia della riga di comando di Azure per distribuire le risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 41d9476d4ce6b7afc2ba9c757e96db7e8e72d5ae
ms.openlocfilehash: a77354d0719240e5916b5ec945dad75e534802d3
ms.lasthandoff: 02/21/2017


---
# <a name="deploy-resources-to-azure"></a>Distribuire risorse in Azure

In questo articolo viene illustrato come distribuire le risorse nella sottoscrizione di Azure. È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per distribuire un modello di Resource Manager che definisce l'infrastruttura per la soluzione.

Per un'introduzione ai concetti di Gestione risorse, vedere [Panoramica di Azure Rseource Manager](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Procedura per la distribuzione

In questo argomento si presuppone che l'utente stia distribuendo il [modello di archiviazione di esempio](#example-storage-template) in questo argomento. È possibile usare un modello diverso, ma i parametri usati saranno diversi da quelli mostrati di seguito.

Dopo aver creato un modello, la procedura generale per la distribuzione del modello consiste in:

1. Accedere all'account
2. Selezionare la sottoscrizione da usare (necessario solo se si dispone di più sottoscrizioni e se si desidera usarne una diversa dalla sottoscrizione predefinita)
3. Creare un gruppo di risorse
4. Distribuire il modello
5. Controllare lo stato della distribuzione

Nelle sezioni seguenti viene illustrato come eseguire questa procedura con [PowerShell](#powershell) o con l'[interfaccia della riga di comando di Azure](#azure-cli).

## <a name="powershell"></a>PowerShell

1. Per installare Azure PowerShell vedere [Get started with Azure PowerShell cmdlets](/powershell/azureps-cmdlets-docs) (Guida introduttiva ai cmdlet di Azure PowerShell).

2. Per iniziare a usare rapidamente la distribuzione, usare i cmdlets seguenti:

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  Il cmdlet `Set-AzureRmContext` è necessario solo se si desidera usare una sottoscrizione diversa dalla sottoscrizione predefinita. Per vedere tutte le sottoscrizioni e i relativi ID, usare:

  ```powershell
  Get-AzureRmSubscription
  ```

3. Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio simile a:

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Per verificare che l'account di archiviazione e il gruppo di risorse siano stati distribuiti per la sottoscrizione, usare:

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Quando si distribuisce un modello, è possibile specificare i parametri del modello come parametri di PowerShell. L'esempio precedente non comprendeva i parametri del modello, pertanto nel modello sono stati usati i valori predefiniti. Per distribuire un altro account di archiviazione e fornire i valori dei parametri per il prefisso del nome di archiviazione e lo SKU dell'account di archiviazione, usare:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Si dispone ora di due account di archiviazione nel gruppo di risorse. 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. Per installare l'interfaccia della riga di comando di Azure, vedere [Install Azure CLI 2.0](/cli/azure/install-az-cli2) (Installare l'interfaccia della riga di comando di Azure 2.0).

2. Per iniziare a usare rapidamente la distribuzione, usare i comandi seguenti:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  Il comando `az account set` è necessario solo se si desidera usare una sottoscrizione diversa dalla sottoscrizione predefinita. Per vedere tutte le sottoscrizioni e i relativi ID, usare:

  ```azurecli
  az account list
  ```

3. Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio simile a:

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Per verificare che l'account di archiviazione e il gruppo di risorse siano stati distribuiti per la sottoscrizione, usare:

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Quando si distribuisce un modello, è possibile specificare i parametri del modello come parametri di PowerShell. L'esempio precedente non comprendeva i parametri del modello, pertanto nel modello sono stati usati i valori predefiniti. Per distribuire un altro account di archiviazione e fornire i valori dei parametri per il prefisso del nome di archiviazione e lo SKU dell'account di archiviazione, usare:

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Si dispone ora di due account di archiviazione nel gruppo di risorse. 

## <a name="example-storage-template"></a>Modello di archiviazione di esempio

Per distribuire un account di archiviazione nella sottoscrizione, usare il modello di esempio seguente:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
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
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso di PowerShell per distribuire i modelli, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](/azure/azure-resource-manager/resource-group-template-deploy).
* Per informazioni dettagliate sull'uso dell'interfaccia della riga di comando di Azure per distribuire i modelli, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](/azure/azure-resource-manager/resource-group-template-deploy-cli).




