---
title: Distribuire un modello di Azure Resource Manager in un runbook di Automazione di Azure
description: Come distribuire un modello di Azure Resource Manager archiviato in Archiviazione di Azure da un runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, automazione di azure
ms.openlocfilehash: 2a6652c988eb77a1c5c7dbf800586b1c5fb756c4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392199"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Distribuire un modello di Azure Resource Manager in un runbook PowerShell di Automazione di Azure

È possibile scrivere un [runbook PowerShell di Automazione di Azure](automation-first-runbook-textual-powershell.md) che distribuisce una risorsa di Azure usando un [modello di Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Con questa operazione è possibile automatizzare la distribuzione delle risorse di Azure. È possibile anche gestire i modelli di Resource Manager in una posizione centrale protetta come Archiviazione di Azure.

In questo articolo si creerà un runbook PowerShell che usa un modello di Resource Manager archiviato in [Archiviazione di Azure](../storage/common/storage-introduction.md) per distribuire un nuovo account di Archiviazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non ne hai ancora uno, puoi [attivare i vantaggi](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) per gli abbonati MSDN o [iscriverti per un account gratuito.](https://azure.microsoft.com/free/)
* [Account di Automazione](automation-sec-configure-azure-runas-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure.  Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* [Account di Archiviazione di Azure](../storage/common/storage-create-storage-account.md) in cui archiviare il modello di Resource Manager
* Azure PowerShell installato in un computer locale. Per informazioni su come ottenere Azure PowerShell, vedere Installare il modulo di [Azure PowerShell.See Install](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) the Azure PowerShell Module for information about how to get Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager

In questo esempio si userà un modello di Resource Manager che distribuisce un nuovo account di Archiviazione di Azure.

In un editor di testo copiare il testo seguente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
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

Salvare il file in locale come **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Salvare il modello di Resource Manager in Archiviazione di Azure

A questo punto si usa PowerShell per creare una condivisione file di Archiviazione di Azure e caricare il file **TemplateTest.json.**
Per istruzioni su come creare una condivisione file e caricare un file nel portale di Azure, vedere [Introduzione ad Archiviazione file di Azure in Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Avviare PowerShell nel computer locale ed eseguire i comandi seguenti per creare una condivisione file e caricare il modello di Resource Manager nella condivisione.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Creare lo script del runbook PowerShell

A questo punto viene creato uno script di PowerShell che ottiene il file TemplateTest.json da Archiviazione di Azure e viene distribuito il modello per creare un nuovo account di Archiviazione di Azure.Now we create a PowerShell script that gets the **TemplateTest.json** file from Azure Storage and deploys the template to create a new Azure Storage account.

In un editor di testo incollare il testo seguente:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Salvare il file in locale come **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importare e pubblicare il runbook nell'account di Automazione di Azure

Si userà ora PowerShell per importare il runbook nell'account di Automazione di Azure e pubblicarlo. Per informazioni su come importare e pubblicare un runbook nel portale di Azure, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).

Per importare DeployTemplate.ps1 nell'account di automazione come runbook di PowerShell, eseguire i comandi di PowerShell seguenti:To import **DeployTemplate.ps1** into your Automation account as a PowerShell runbook, run the following PowerShell commands:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Avviare il runbook

A questo punto si avvia il runbook chiamando il cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) . Per informazioni su come avviare un runbook nel portale di Azure, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).

Eseguire i comandi seguenti nella console di PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Il runbook viene eseguito ed è possibile verificarne lo stato eseguendo `$job.Status`.

Il runbook ottiene il modello di Resource Manager e lo usa per distribuire un nuovo account di Archiviazione di Azure.
È possibile verificare che il nuovo account di archiviazione sia stato creato eseguendo il comando seguente:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Summary

L'operazione è terminata. Ora è possibile usare Automazione di Azure e Archiviazione di Azure con i modelli di Resource Manager per distribuire tutte le risorse di Azure.Now you can use Azure Automation and Azure Storage with Resource Manager templates to deploy all your Azure resources.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui modelli di Resource Manager, vedere [Panoramica](../azure-resource-manager/management/overview.md)di Azure Resource Manager.To learn more about Resource Manager templates, see Azure Resource Manager overview .
* Per informazioni introduttive su Archiviazione di Azure, vedere [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md).
* Per trovare altri runbook utili di Automazione di Azure, vedere [Raccolte di runbook e moduli per l'automazione di Azure](automation-runbook-gallery.md).
* Per trovare altri modelli utili di Resource Manager, vedere [Modelli di Avvio rapido](https://azure.microsoft.com/resources/templates/)di Azure .
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
