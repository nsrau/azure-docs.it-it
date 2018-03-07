---
title: Esportare il modello di Resource Manager con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Usare Azure Resource Manager e l'interfaccia della riga di comando di Azure per esportare un modello da un gruppo di risorse.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: 15e7e811c7cb1777e34f1bfb629fa24a60f9e5cb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Esportare il modello di Azure Resource Manager con l'interfaccia della riga di comando di Azure

Resource Manager consente di esportare un modello di Resource Manager dalle risorse esistenti nella sottoscrizione. Il modello generato può essere usato per ottenere informazioni sulla sintassi del modello o per automatizzare la ridistribuzione della soluzione in base alle esigenze.

Si noti che è possibile eseguire l'esportazione di un modello in due modi diversi:

* È possibile esportare il **modello effettivo usato per una distribuzione**. Il modello esportato include tutti i parametri e le variabili uguali a quelli visualizzati nel modello originale. Questo approccio è utile quando si vuole recuperare un modello.
* È possibile esportare un **modello generato che rappresenta lo stato corrente del gruppo di risorse**. Il modello esportato non si basa su un modello qualsiasi usato per la distribuzione, ma crea un modello che è uno "snapshot" o un "backup" del gruppo di risorse. Il modello esportato ha diversi valori hardcoded e probabilmente meno parametri di quelli che si definiscono in genere. Usare questa opzione per ridistribuire le risorse allo stesso gruppo di risorse. Per usare questo modello per un altro gruppo di risorse, è necessario apportare alcune importanti modifiche.

Questo articolo illustra entrambi gli approcci.

## <a name="deploy-a-solution"></a>Distribuire una soluzione

Per illustrare entrambi gli approcci per l'esportazione di un modello, si inizia con la distribuzione di una soluzione nella sottoscrizione in uso. Se si dispone già di un gruppo di risorse nella sottoscrizione che si vuole esportare, non è necessario distribuire la soluzione. Il resto dell'articolo si riferisce tuttavia al modello per questa soluzione. Lo script di esempio distribuisce un account di archiviazione.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Salvare un modello dalla cronologia di distribuzione

È possibile recuperare un modello dalla cronologia della distribuzione usando il comando [az group deployment export](/cli/azure/group/deployment#az_group_deployment_export). L'esempio seguente salva il modello precedentemente distribuito:

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

Restituisce il modello. Copiare il codice JSON e salvarlo come file. Si noti che è il modello esatto usato per la distribuzione. I parametri e le variabili corrispondono al modello proveniente da GitHub. È possibile ridistribuire il modello.


## <a name="export-resource-group-as-template"></a>Esportare un gruppo di risorse come modello

Invece di recuperare un modello dalla cronologia della distribuzione, è possibile recuperarne uno che rappresenta lo stato corrente di un gruppo di risorse tramite il comando [az group export](/cli/azure/group#az_group_export). Usare questo comando quando sono state apportate molte modifiche al gruppo di risorse e nessun modello esistente rappresenta tutte le modifiche. Si tratta di uno snapshot del gruppo di risorse, che è possibile usare per la ridistribuzione allo stesso gruppo di risorse. Per usare il modello esportato per altre soluzioni, è necessario apportare alcune importanti modifiche.

```azurecli
az group export --name ExampleGroup
```

Restituisce il modello. Copiare il codice JSON e salvarlo come file. Si noti che è diverso rispetto al modello in GitHub. Il modello contiene parametri diversi e non include variabili. I valori dello SKU e della posizione della risorsa di archiviazione sono impostati come hardcoded. L'esempio seguente mostra il modello esportato, ma il modello in uso presenta un nome di parametro leggermente diverso:

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

È possibile ridistribuire questo modello, ma è necessario individuare un nome univoco per l'account di archiviazione. Il nome del parametro è leggermente diverso.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Personalizzare il modello esportato

È possibile modificare questo modello per renderlo più flessibile e facile da usare. Per consentire più posizioni, modificare la proprietà location affinché usi la stessa posizione del gruppo di risorse:

```json
"location": "[resourceGroup().location]",
```

Per evitare di dover individuare un nome univoco per l'account di archiviazione, rimuovere il parametro per il nome dell'account di archiviazione. Aggiungere un parametro per il suffisso del nome di archiviazione e uno SKU di archiviazione:

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
* Per altre informazioni sull'uso del portale per esportare un modello, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).