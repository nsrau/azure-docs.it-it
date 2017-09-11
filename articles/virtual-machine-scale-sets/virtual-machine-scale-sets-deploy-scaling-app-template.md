---
title: "Distribuire un'app in un set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Informazioni su come distribuire una semplice applicazione di scalabilità automatica in un set di scalabilità di macchine virtuali usando un modello di Azure Resource Manager."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-an-autoscaling-app-using-a-template"></a>Distribuire un'app di scalabilità automatica usando un modello

I [modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) sono un ottimo modo di distribuire gruppi di risorse correlate. Questa esercitazione spiega come [distribuire un semplice set di scalabilità](virtual-machine-scale-sets-mvss-start.md) e descrive come distribuire una semplice applicazione di scalabilità automatica in un set di scalabilità usando un modello di Azure Resource Manager.  È anche possibile configurare la scalabilità automatica usando PowerShell, l'interfaccia della riga di comando o il portale. Per altre informazioni, vedere la [panoramica del ridimensionamento automatico](virtual-machine-scale-sets-autoscale-overview.md).

## <a name="two-quickstart-templates"></a>Due modelli di avvio rapido
Quando si distribuisce un set di scalabilità, è possibile installare nuovo software in un'immagine della piattaforma usando un'[estensione della macchina virtuale](../virtual-machines/virtual-machines-windows-extensions-features.md). Le estensioni della macchina virtuale sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure, come ad esempio la distribuzione di un'app. Il [repository Azure/azure-quickstart-templates di GitHub](https://github.com/Azure/azure-quickstart-templates) include due diversi modelli di esempio che illustrano come distribuire un'applicazione di scalabilità automatica in un set di scalabilità usando le estensioni della macchina virtuale.

### <a name="python-http-server-on-linux"></a>Server HTTP Python in Linux
Il modello di esempio [server HTTP Python in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) consente di distribuire una semplice applicazione di scalabilità automatica in esecuzione in un set di scalabilità di Linux.  [Bottle](http://bottlepy.org/docs/dev/), un framework Web Python, e un semplice server HTTP vengono distribuiti in ogni macchina virtuale del set di scalabilità usando un'estensione script personalizzata della macchina virtuale. Le prestazioni del set di scalabilità aumentano quando l'utilizzo medio della CPU tra tutte le macchine virtuali è superiore al 60% e vengono ridotte quando l'utilizzo medio della CPU è inferiore al 30%.

Oltre alla risorsa del set di scalabilità, il modello di esempio *azuredeploy.json* dichiara anche le risorse di impostazioni di rete virtuale, indirizzo IP pubblico, bilanciamento del carico e scalabilità automatica.  Per altre informazioni sulla creazione di queste risorse in un modello, vedere [Set di scalabilità Linux con scalabilità automatica](virtual-machine-scale-sets-linux-autoscale.md).

Nel modello *azuredeploy.json* la proprietà `extensionProfile` della risorsa `Microsoft.Compute/virtualMachineScaleSets` specifica un'estensione script personalizzata. `fileUris` specifica la posizione degli script. In questo caso si tratta di due file: *workserver.py*, che definisce un semplice server HTTP, e *installserver.sh*, che installa Bottle e avvia il server HTTP. `commandToExecute` specifica il comando da eseguire dopo la distribuzione del set di scalabilità.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Applicazione ASP.NET MVC in Windows
Il modello di esempio [applicazione ASP.NET MVC in Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) consente di distribuire una semplice app ASP.NET MVC in esecuzione in IIS nel set di scalabilità di Windows.  IIS e l'app MVC vengono distribuiti tramite l'estensione della macchina virtuale [Configurazione dello stato desiderato (DSC) di PowerShell](virtual-machine-scale-sets-dsc.md).  Le prestazioni del set di scalabilità aumentano (in una sola istanza di macchina virtuale per volta) quando l'utilizzo della CPU è superiore al 50% per 5 minuti. 

Oltre alla risorsa del set di scalabilità, il modello di esempio *azuredeploy.json* dichiara anche le risorse di impostazioni di rete virtuale, indirizzo IP pubblico, bilanciamento del carico e scalabilità automatica. Questo modello illustra anche l'aggiornamento dell'applicazione.  Per altre informazioni sulla creazione di queste risorse in un modello, vedere [Set di scalabilità Windows con scalabilità automatica](virtual-machine-scale-sets-windows-autoscale.md).

Nel modello *azuredeploy.json*, la proprietà `extensionProfile` della risorsa `Microsoft.Compute/virtualMachineScaleSets` specifica un'estensione [configurazione dello stato desiderato (DSC)](virtual-machine-scale-sets-dsc.md) che consente di installare IIS e un'app Web predefinita da un pacchetto WebDeploy.  Lo script *IISInstall.ps1* consente di installare IIS nella macchina virtuale e si trova nella cartella *DSC*.  L'app Web MVC si trova nella cartella *WebDeploy*.  I percorsi per lo script di installazione e l'app Web sono definiti nei parametri `powershelldscZip` e `webDeployPackage` del file *azuredeploy.parameters.json*. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>Distribuire il modello
Il modo più semplice per distribuire il modello [server HTTP Python in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) o [applicazione ASP.NET MVC in Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) consiste nell'usare il pulsante **Distribuisci in Azure**, presente nei file leggimi in GitHub.  È inoltre possibile usare PowerShell o l'interfaccia della riga di comando di Azure per distribuire i modelli di esempio.

### <a name="powershell"></a>PowerShell
Copiare i file del [server HTTP Python in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) o dell'[applicazione ASP.NET MVC in Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) dal repository GitHub a una cartella nel computer locale.  Aprire il file *azuredeploy.parameters.json* e aggiornare i valori predefiniti dei parametri `vmssName`, `adminUsername` e `adminPassword`. Salvare lo script di PowerShell seguente come *deploy.ps1* nella cartella di esempio come modello *azuredeploy.json*. Per distribuire il modello di esempio, eseguire lo script *deploy.ps1* da una finestra di comando di PowerShell.

```powershell
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
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
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

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

