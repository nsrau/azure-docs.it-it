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
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: ea42f634fabbbaf13f0d97c71a9cb8d1d8102dea
ms.lasthandoff: 03/16/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md)
> * [Portale](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Questo articolo illustra come usare Azure PowerShell con modelli di Resource Manager per distribuire risorse in Azure. Il modello può essere un file locale oppure un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

## <a name="deploy"></a>Distribuire
* Per iniziare rapidamente la distribuzione, usare i comandi seguenti per distribuire un modello locale con parametri inline:

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {your-subscription-ID}
  New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
  ```

  Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio che include il risultato:

  ```powershell
  ProvisioningState       : Succeeded
  ```

* Il cmdlet `Set-AzureRmContext` è necessario solo se si desidera usare una sottoscrizione diversa dalla sottoscrizione predefinita. Per vedere tutte le sottoscrizioni e i relativi ID, usare:

  ```powershell
  Get-AzureRmSubscription
  ```

* Per distribuire un modello esterno, usare il parametro **TemplateUri**:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
  ```

* Per passare i valori dei parametri in un file, usare il parametro **TemplateParameterFile**:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json -TemplateParameterFile c:\MyTemplates\storage.parameters.json
  ```

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

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Per usare la modalità completa, usare il parametro **Mode**:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="deploy-private-template-with-sas-token"></a>Distribuire il modello privato con token SAS
È possibile aggiungere i modelli a un account di archiviazione e collegarli durante la distribuzione con un token SAS.

> [!IMPORTANT]
> Attenendosi alla seguente procedura, il BLOB contenente il modello sarà accessibile solo da parte del proprietario dell'account. Tuttavia, quando si crea un token di firma di accesso condiviso per il BLOB, quest'ultimo sarà accessibile a tutti gli utenti con quell'URI. Se l'URI viene intercettato da un altro utente, quest'ultimo sarà in grado di accedere al modello. Utilizzare un token di firma di accesso condiviso è un buon metodo per limitare l'accesso ai modelli, ma è necessario non includere direttamente nel modello dati sensibili come le password.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Aggiungere un modello privato all'account di archiviazione
L'esempio seguente configura un contenitore dell'account di archiviazione privato e carica un modello:
   
```powershell
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

### <a name="provide-sas-token-during-deployment"></a>Fornire il token SAS in fase di distribuzione
Per distribuire un modello privato in un account di archiviazione, generare un token di firma di accesso condiviso e includerlo nell'URI del modello. Impostare l'ora di scadenza in modo da garantire un tempo sufficiente per completare la distribuzione.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Per un esempio sull'uso di un token di firma di accesso condiviso con modelli collegati, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>Parametri
   
Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando di PowerShell, viene richiesto di fornire un valore per il parametro. Azure PowerShell presenta il parametro incluso nel modello con il suffisso **FromTemplate**. Ad esempio, un parametro denominato **ResourceGroupName** nel modello sarà in conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Verrà quindi richiesto di fornire un valore per **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

È possibile usare i parametri inline e un file di parametri locale nella stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori inline durante la distribuzione. Se si specificano valori per un parametro sia nel file dei parametri locale che inline, il valore inline ha la precedenza.

Tuttavia, quando si usa un file di parametri esterni, non è possibile trasmettere altri valori, né inline né da un file locale. Quando si specifica un file di parametri nel parametro **TemplateParameterUri**, tutti i parametri inline vengono ignorati. È necessario fornire tutti i valori dei parametri presenti nel file esterno. Se il modello include un valore importante che non è possibile includere nel file dei parametri, aggiungere tale valore a un insieme di credenziali delle chiavi oppure fornire inline tutti valori dei parametri in modo dinamico.

## <a name="debug"></a>Debug

Per registrare informazioni aggiuntive sulla distribuzione che potrebbero contribuire a risolvere eventuali errori di distribuzione, usare il parametro **DeploymentDebugLogLevel**. È possibile specificare la registrazione del contenuto della richiesta, del contenuto della risposta o di entrambi con l'operazione di distribuzione.
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

Per ottenere informazioni dettagliate su un'operazione di distribuzione non riuscita, usare:

```powershell
(Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="complete-deployment-script"></a>Completare lo script di distribuzione

L'esempio seguente mostra lo script di PowerShell per la distribuzione di un modello che viene generato dalla funzionalità di [esportazione modello](resource-manager-export-template.md):

```powershell
<#
 .SYNOPSIS
    Deploys a template to Azure

 .DESCRIPTION
    Deploys an Azure Resource Manager template

 .PARAMETER subscriptionId
    The subscription id where the template will be deployed.

 .PARAMETER resourceGroupName
    The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

 .PARAMETER resourceGroupLocation
    Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

 .PARAMETER deploymentName
    The deployment name.

 .PARAMETER templateFilePath
    Optional, path to the template file. Defaults to template.json.

 .PARAMETER parametersFilePath
    Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
#>

param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @();
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
``` 

## <a name="next-steps"></a>Passaggi successivi
* Per un esempio di distribuzione delle risorse con la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
* Per una serie di quattro parti sull'automazione della distribuzione, vedere [Automazione della distribuzione di applicazioni nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questa serie illustra argomenti come architettura, accesso e sicurezza, disponibilità, scalabilità e distribuzione delle applicazioni.


