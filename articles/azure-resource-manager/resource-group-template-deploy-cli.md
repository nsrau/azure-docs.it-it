---
title: Distribuire le risorse con il modello e l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Utilizzare Azure Resource Manager e l&quot;interfaccia della riga di comando di Azure per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure

Questo argomento illustra come usare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) con modelli di Resource Manager per distribuire risorse in Azure.  Il modello può essere un file locale oppure un file esterno disponibile tramite un URI.

È possibile ottenere il modello (storage.json) usato in questi esempi nell'articolo [Creare il primo modello di Azure Resource Manager](resource-manager-create-first-template.md#final-template). Per usare il modello con questi esempi, creare un file JSON e aggiungere il contenuto copiato.

## <a name="deploy-local-template"></a>Distribuire un modello locale

Per iniziare rapidamente la distribuzione, usare i comandi seguenti per distribuire un modello locale con parametri inline:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio che include il risultato:

```azurecli
"provisioningState": "Succeeded",
```

L'esempio precedente ha creato il gruppo di risorse nella sottoscrizione predefinita. Per usare una sottoscrizione diversa, aggiungere il comando [az account set](/cli/azure/account#set) dopo l'accesso.

## <a name="deploy-external-template"></a>Distribuire un modello esterno

Per distribuire un modello esterno, usare il parametro **template-uri**. Il modello può essere qualsiasi URI accessibile pubblicamente (ad esempio un file nell'account di archiviazione).
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

È possibile proteggere il modello richiedendo un token di firma di accesso condiviso per l'accesso. Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-cli-sas-token.md).

## <a name="parameter-files"></a>File dei parametri

Negli esempi precedenti è stato mostrano come passare i parametri come valori inline. È possibile specificare i valori dei parametri in un file e passare tale file durante la distribuzione. 

Il file dei parametri deve essere nel formato seguente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

Per passare un file dei parametri locale, usare:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>Testare una distribuzione

Per testare il modello e i valori dei parametri senza distribuire effettivamente le risorse, usare il comando [az group deployment validate](/cli/azure/group/deployment#validate). Sono disponibili le stesse opzioni per l'uso di file locali o remoti.

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>Debug

Per visualizzare informazioni sulle operazioni per una distribuzione non riuscita, usare:
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).


## <a name="export-resource-manager-template"></a>Esportare un modello di Resource Manager
Per un gruppo di risorse esistente, distribuito mediante l'interfaccia della riga di comando di Azure o uno degli altri metodi come il portale, è possibile visualizzare il modello di Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione, perché tutti gli elementi dell'infrastruttura sono definiti nel modello.
2. È possibile acquisire familiarità con la sintassi del modello esaminando il codice JSON (JavaScript Object Notation) che rappresenta la soluzione.

Per visualizzare il modello per un gruppo di risorse, eseguire il comando [az group export](/cli/azure/group#export).

```azurecli
az group export --name ExampleGroup
```

Per altre informazioni, vedere [Export an Azure Resource Manager template from existing resources](resource-manager-export-template.md) (Esportare un modello di Azure Resource Manager da risorse esistenti).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Per usare la modalità completa, usare il parametro `mode`:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>Passaggi successivi
* Per uno script di esempio completo che consente di distribuire un modello, vedere lo [script di distribuzione di modelli di Resource Manager](resource-manager-samples-cli-deploy.md).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-cli-sas-token.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

