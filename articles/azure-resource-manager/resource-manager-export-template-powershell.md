---
title: Esportare un modello di Resource Manager con Azure PowerShell | Documentazione Microsoft
description: Usare Azure Resource Manager e Azure PowerShell per esportare un modello da un gruppo di risorse.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 74bf865d796c965b4843092215639b32f033d807
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017

---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>Esportare modelli di Azure Resource Manager con PowerShell

Resource Manager consente di esportare un modello di Resource Manager dalle risorse esistenti nella sottoscrizione. Il modello generato può essere usato per ottenere informazioni sulla sintassi del modello o per automatizzare la ridistribuzione della soluzione in base alle esigenze.

È importante notare che è possibile esportare un modello in due modi diversi:

* È possibile esportare il modello vero e proprio usato per una distribuzione. Il modello esportato include tutti i parametri e le variabili uguali a quelli visualizzati nel modello originale. Questo approccio è utile quando si vuole recuperare un modello.
* È possibile esportare un modello che rappresenta lo stato attuale del gruppo di risorse. Il modello esportato non si basa su un modello qualsiasi usato per la distribuzione, ma crea un modello che è uno snapshot del gruppo di risorse. Il modello esportato ha diversi valori hardcoded e probabilmente meno parametri di quelli che si definiscono in genere. Questo approccio è utile quando è stato modificato il gruppo di risorse e in seguito è necessario acquisire il gruppo di risorse come modello.

Questo argomento illustra entrambi gli approcci.

## <a name="deploy-a-solution"></a>Distribuire una soluzione

Per illustrare entrambi gli approcci all'esportazione di un modello, verrà mostrata la distribuzione di una soluzione nella sottoscrizione. Se nella sottoscrizione è già presente un gruppo di risorse da esportare, la distribuzione della soluzione non è necessaria. Il resto di questo articolo, tuttavia, fa riferimento al modello per questa soluzione. Lo script di esempio consente di distribuire un account di archiviazione.

```powershell
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Salvare il modello dalla cronologia di distribuzione

È possibile recuperare un modello dalla cronologia della distribuzione usando il comando [Save-AzureRmResourceGroupDeploymentTemplate](/powershell/module/azurerm.resources/save-azurermresourcegroupdeploymenttemplate). L'esempio seguente illustra come salvare il modello distribuito in precedenza:

```powershell
Save-AzureRmResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

Restituisce il percorso del modello.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Aprendo il file si nota che si tratta dello stesso modello usato per la distribuzione. I parametri e le variabili corrispondono al modello di GitHub. Questo modello può essere ridistribuito.

## <a name="export-resource-group-as-template"></a>Esportare il gruppo di risorse come modello

Invece di recuperare un modello dalla cronologia di distribuzione, è possibile recuperarne uno che rappresenta lo stato attuale di un gruppo di risorse usando il comando [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup). Il comando è utile quando sono state apportate numerose modifiche al gruppo di risorse e nessun modello esistente è in grado di rappresentarle tutte.

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleGroup
```

Restituisce il percorso del modello.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Aprendo il file si nota che è diverso rispetto al modello di GitHub. Contiene parametri diversi e non include variabili. La risorsa di archiviazione SKU e la posizione sono hardcoded in base ai valori. L'esempio seguente mostra il modello esportato, ma il modello dell'utente presenta un nome di parametro diverso:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

È possibile ridistribuire questo modello, ma è necessario individuare un nome univoco per l'account di archiviazione. Il nome del parametro è leggermente diverso.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
```

## <a name="customize-exported-template"></a>Personalizzare il modello esportato

È possibile modificare questo modello per renderlo più flessibile e facile da usare. Per consentire più posizioni, modificare la proprietà location affinché usi la stessa posizione del gruppo di risorse:

```json
"location": "[resourceGroup().location]",
```

Per evitare di dover individuare un nome univoco per l'account di archiviazione, rimuovere il parametro del nome dell'account di archiviazione. Aggiungere un parametro per il suffisso del nome di archiviazione e uno SKU di archiviazione:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Aggiungere una variabile che costruisce il nome dell'account di archiviazione con la funzione uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Impostare il nome dell'account di archiviazione sulla variabile:

```json
"name": "[variables('storageAccountName')]",
```

Impostare lo SKU sul parametro:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

Il modello si presenta ora come segue:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Ridistribuire il modello modificato.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'utilizzo del portale per esportare un modello, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

