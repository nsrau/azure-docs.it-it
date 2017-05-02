---
title: Distribuire risorse con PowerShell e i modelli | Microsoft Docs
description: Utilizzare Azure Resource Manager e Azure PowerShell per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell

Questo articolo illustra come usare Azure PowerShell con modelli di Resource Manager per distribuire risorse in Azure. Il modello può essere un file locale oppure un file esterno disponibile tramite un URI.

È possibile ottenere il modello (storage.json) usato in questi esempi nell'articolo [Creare il primo modello di Azure Resource Manager](resource-manager-create-first-template.md#final-template). Per usare il modello con questi esempi, creare un file JSON e aggiungere il contenuto copiato.

## <a name="deploy-local-template"></a>Distribuire un modello locale
Per iniziare rapidamente la distribuzione, usare i cmdlet seguenti per distribuire un modello locale con parametri inline. 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio che include il risultato:

```powershell
ProvisioningState       : Succeeded
```

L'esempio precedente ha creato il gruppo di risorse nella sottoscrizione predefinita. Per usare una sottoscrizione diversa, aggiungere il cmdlet [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) dopo l'accesso.

## <a name="deploy-external-template"></a>Distribuire un modello esterno

Per distribuire un modello esterno, usare il parametro **TemplateUri**. Il modello può essere qualsiasi URI accessibile pubblicamente (ad esempio un file nell'account di archiviazione).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

È possibile proteggere il modello richiedendo un token di firma di accesso condiviso per l'accesso. Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-powershell-sas-token.md).

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

Per passare un file dei parametri locale, usare il parametro **TemplateParameterFile**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Per passare un file dei parametri esterno, usare il parametro **TemplateParameterUri**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

È possibile usare i parametri inline e un file di parametri locale nella stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori inline durante la distribuzione. Se si specificano valori per un parametro sia nel file dei parametri locale che inline, il valore inline ha la precedenza.

Tuttavia, quando si usa un file di parametri esterni, non è possibile trasmettere altri valori, né inline né da un file locale. Quando si specifica un file di parametri nel parametro **TemplateParameterUri**, tutti i parametri inline vengono ignorati. È necessario fornire tutti i valori dei parametri presenti nel file esterno. Se il modello include un valore importante che non è possibile includere nel file dei parametri, aggiungere tale valore a un insieme di credenziali delle chiavi oppure fornire inline tutti valori dei parametri in modo dinamico.

Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando di PowerShell, PowerShell aggiunge al parametro del modello il suffisso **FromTemplate**. Ad esempio, un parametro denominato **ResourceGroupName** nel modello sarà in conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Verrà quindi richiesto di fornire un valore per **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

## <a name="test-a-deployment"></a>Testare una distribuzione

Per testare il modello e i valori dei parametri senza distribuire effettivamente le risorse, usare [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). Sono disponibili le stesse opzioni per l'uso di file locali o remoti.

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>Registrare il contenuto della distribuzione per il debug

Le informazioni sulle operazioni di distribuzione vengono automaticamente registrate nei log attività. Tuttavia, per registrare informazioni aggiuntive sulla distribuzione che potrebbero essere utili per la risoluzione di eventuali errori di distribuzione, usare il parametro **DeploymentDebugLogLevel**. È possibile specificare la registrazione del contenuto della richiesta, del contenuto della risposta o di entrambi con l'operazione di distribuzione.
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

Per altre informazioni sulla visualizzazione dei log attività, vedere [Visualizzare i log attività per controllare le azioni sulle risorse](resource-group-audit.md).

## <a name="export-resource-manager-template"></a>Esportare un modello di Resource Manager
Per un gruppo di risorse esistente, distribuito mediante PowerShell o uno degli altri metodi quali il portale, è possibile visualizzare il modello di Azure Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione, perché tutti gli elementi dell'infrastruttura sono definiti nel modello.
2. È possibile acquisire familiarità con la sintassi del modello esaminando il codice JSON (JavaScript Object Notation) che rappresenta la soluzione.

Per visualizzare il modello per un gruppo di risorse, eseguire il cmdlet [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) .

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

Per altre informazioni, vedere [Export an Azure Resource Manager template from existing resources](resource-manager-export-template.md) (Esportare un modello di Azure Resource Manager da risorse esistenti).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Per usare la modalità completa, usare il parametro `Mode`:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>Passaggi successivi
* Per uno script di esempio completo che consente di distribuire un modello, vedere lo [script di distribuzione di modelli di Resource Manager](resource-manager-samples-powershell-deploy.md).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-powershell-sas-token.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).


