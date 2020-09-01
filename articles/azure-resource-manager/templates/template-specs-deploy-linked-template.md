---
title: Distribuire una specifica di modello come modello collegato
description: Informazioni su come distribuire una specifica di modello esistente in una distribuzione collegata.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 799dac2bb553983b16f734d1d1abc2ad9281fb58
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228019"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Esercitazione: distribuire una specifica di modello come modello collegato (anteprima)

Informazioni su come distribuire una [specifica di modello](template-specs.md) esistente usando una [distribuzione collegata](linked-templates.md#linked-template). Si usano le specifiche del modello per condividere i modelli ARM con altri utenti nell'organizzazione. Dopo aver creato una specifica del modello, è possibile distribuire la specifica del modello usando Azure PowerShell o l'interfaccia della riga di comando di Azure. È anche possibile distribuire la specifica del modello come parte della soluzione usando un modello collegato.

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> La funzionalità Specifiche di modello è attualmente in anteprima. Per usarlo, è necessario [iscriversi per l'anteprima](https://aka.ms/templateSpecOnboarding).

## <a name="create-a-template-spec"></a>Creare una specifica del modello

Seguire la [Guida introduttiva: creare e distribuire una specifica del modello](quickstart-create-template-specs.md) per creare una specifica del modello per la distribuzione di un account di archiviazione. Nella sezione successiva sono necessari il nome del gruppo di risorse della specifica del modello, il nome della specifica del modello e la versione della specifica del modello.

## <a name="create-the-main-template"></a>Creare il modello principale

Per distribuire una specifica di modello in un modello ARM, aggiungere una [risorsa distribuzioni](/azure/templates/microsoft.resources/deployments) al modello principale. Nella `templateLink` Proprietà specificare l'ID risorsa di una specifica del modello. creare un modello con il codice JSON seguente denominato **azuredeploy.json**. In questa esercitazione si presuppone che sia stato salvato in un percorso **c:\Templates\deployTS\azuredeploy.js** , ma è possibile utilizzare qualsiasi percorso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

L'ID della specifica del modello viene generato utilizzando la [`resourceID()`](template-functions-resource.md#resourceid) funzione. L'argomento del gruppo di risorse nella funzione resourceID () è facoltativo se templateSpec è nello stesso gruppo di risorse della distribuzione corrente.  È anche possibile passare direttamente l'ID risorsa come parametro. Per ottenere l'ID, usare:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Version.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Si è verificato un problema noto con l'ottenimento di un ID specifiche del modello e l'assegnazione a una variabile in Windows PowerShell.

---

La sintassi per il passaggio di parametri alla specifica del modello è la seguente:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> Il apiVersion di `Microsoft.Resources/deployments` deve essere 2020-06-01 o versione successiva.

## <a name="deploy-the-template"></a>Distribuire il modello

Quando si distribuisce il modello collegato, viene distribuita l'applicazione Web e l'account di archiviazione. La distribuzione è identica alla distribuzione di altri modelli ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json"

```

---

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare una specifica di modello che include modelli collegati, vedere [Creare una specifica di modello di un modello collegato](template-specs-create-linked.md).
