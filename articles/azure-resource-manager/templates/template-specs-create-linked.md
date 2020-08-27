---
title: Creare una specifica di modello con i modelli collegati
description: Informazioni su come creare una specifica di modello con i modelli collegati.
ms.topic: conceptual
ms.date: 08/27/2020
ms.openlocfilehash: a70d4be2810b8d5a19fa9d806444cac1674e1a05
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959222"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Esercitazione: creare una specifica di modello con i modelli collegati (anteprima)

Informazioni su come creare una [specifica del modello](template-specs.md) con un [modello collegato](linked-templates.md#linked-template). Si usano le specifiche del modello per condividere i modelli ARM con altri utenti nell'organizzazione. Questo articolo illustra come creare una specifica del modello per creare un pacchetto di un modello principale e dei relativi modelli collegati usando la nuova `relativePath` proprietà della [risorsa di distribuzione](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> La funzionalità Specifiche di modello è attualmente in anteprima. Per usarlo, è necessario [iscriversi per l'anteprima](https://aka.ms/templateSpecOnboarding).

## <a name="create-linked-templates"></a>Creare modelli collegati

Creare il modello principale e il modello collegato.

Per collegare un modello, aggiungere una [risorsa distribuzioni](/azure/templates/microsoft.resources/deployments) al modello principale. Nella `templateLink` Proprietà specificare il percorso relativo del modello collegato in base al percorso del modello padre.

Il modello collegato viene chiamato **linkedTemplate.json**e viene archiviato in una sottocartella denominata **artefatti** nel percorso in cui è archiviato il modello principale.  È possibile usare uno dei valori seguenti per relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

La `relativePath` proprietà è sempre relativa al file di modello in cui `relativePath` è dichiarato, quindi se è presente un altro linkedTemplate2.jsin che viene chiamato da linkedTemplate.json e linkedTemplate2.json viene archiviato nella stessa sottocartella degli artefatti, il relativePath specificato in linkedTemplate.json è semplicemente `linkedTemplate2.json` .

1. Creare il modello principale con il codice JSON seguente. Salvare il modello principale come **azuredeploy.jsnel** computer locale. In questa esercitazione si presuppone che sia stato salvato in un percorso **c:\Templates\linkedTS\azuredeploy.js** , ma è possibile utilizzare qualsiasi percorso.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > Il apiVersion di `Microsoft.Resources/deployments` deve essere 2020-06-01 o versione successiva.

1. Creare una directory denominata **artefatti** nella cartella in cui è salvato il modello principale.
1. Creare il modello collegato con il codice JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
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

1. Salvare il modello come **linkedTemplate.js** nella cartella **artefatti** .

## <a name="create-template-spec"></a>Creare la specifica di modello

Le specifiche di modelli vengono archiviate in gruppi di risorse.  Creare un gruppo di risorse e quindi creare una specifica del modello con lo script seguente. Il nome della specifica del modello è **webspec**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az template-specs create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

Al termine, è possibile visualizzare la specifica del modello dal portale di Azure o usando il cmdlet seguente:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Distribuire la specifica di modello

È ora possibile distribuire la specifica di modello. La distribuzione della specifica di modello è analoga alla distribuzione del modello che contiene, con la differenza che occorre passare l'ID risorsa della specifica di modello. Usare gli stessi comandi di distribuzione e, se necessario, passare i valori dei parametri della specifica di modello.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Si è verificato un problema noto con l'ottenimento di un ID specifiche del modello e l'assegnazione a una variabile in Windows PowerShell.

---

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla distribuzione di una specifica di modello come modello collegato, vedere [esercitazione: distribuire una specifica di modello come modello collegato](template-specs-deploy-linked-template.md).
