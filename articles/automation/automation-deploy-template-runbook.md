---
title: Distribuire un modello di Azure Resource Manager in un runbook di Automazione di Azure | Microsoft Docs
description: Come distribuire un modello di Azure Resource Manager archiviato in Archiviazione di Azure da un runbook
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: powershell, runbook, json, automazione di azure
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 07/09/2017
ms.author: eslesar
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 6f7df68f8bc594ebd2b58798f02c1c513c0d86c7
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Distribuire un modello di Azure Resource Manager in un runbook PowerShell di Automazione di Azure

È possibile scrivere un [runbook PowerShell di Automazione di Azure](automation-first-runbook-textual-powershell.md) che distribuisce una risorsa di Azure usando un [modello di Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Con questa operazione è possibile automatizzare la distribuzione delle risorse di Azure. È possibile anche gestire i modelli di Resource Manager in una posizione centrale protetta come Archiviazione di Azure.

In questo argomento si creerà un runbook PowerShell che usa un modello di Resource Manager archiviato in [Archiviazione di Azure](../storage/storage-introduction.md) per distribuire un nuovo account di Archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure <a href="/pricing/free-account/" target="_blank">[iscriversi per ottenere un account gratuito](https://azure.microsoft.com/free/).
* [Account di Automazione](automation-sec-configure-azure-runas-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure.  Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* [Account di Archiviazione di Azure](../storage/storage-create-storage-account.md) in cui archiviare il modello di Resource Manager
* Azure Powershell installato in un computer locale. Per informazioni sulla configurazione di PowerShell, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0).

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
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
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

Salvare il file in locale come `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Salvare il modello di Resource Manager in Archiviazione di Azure

Si userà ora PowerShell per creare una condivisione file di Archiviazione di Azure e caricare il file `TemplateTest.json`.
Per istruzioni su come creare una condivisione file e caricare un file nel portale di Azure, vedere [Introduzione ad Archiviazione file di Azure in Windows](../storage/storage-dotnet-how-to-use-files.md).

Avviare PowerShell nel computer locale ed eseguire i comandi seguenti per creare una condivisione file e caricare il modello di Resource Manager nella condivisione.

```powershell
# Login to Azure
Login-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Creare lo script del runbook PowerShell

Si creerà ora uno script di PowerShell che ottiene il file `TemplateTest.json` da Archiviazione di Azure e distribuisce il modello per creare un nuovo account di Archiviazione di Azure.

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
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Salvare il file in locale come `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importare e pubblicare il runbook nell'account di Automazione di Azure

Si userà ora PowerShell per importare il runbook nell'account di Automazione di Azure e pubblicarlo.
Per informazioni su come importare e pubblicare un runbook nel portale di Azure, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md).

Per importare `DeployTemplate.ps1` nell'account di Automazione come un runbook di PowerShell, eseguire i comandi di PowerShell seguenti:

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
Import-AzureRmAutomationRunbook @

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Avviare il runbook

Si avvierà ora il runbook chiamando il cmdlet [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0).

Per informazioni su come avviare un runbook nel portale di Azure, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).

Eseguire i comandi seguenti nella console di PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Il runbook viene eseguito ed è possibile verificarne lo stato eseguendo `$job.Status`.

Il runbook ottiene il modello di Resource Manager e lo usa per distribuire un nuovo account di Archiviazione di Azure.
È possibile verificare che il nuovo account di archiviazione sia stato creato eseguendo il comando seguente:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Riepilogo

L'operazione è terminata. Sarà ora possibile usare Automazione di Azure, Archiviazione di Azure e modelli di Resource Manager per distribuire tutte le risorse di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui modelli di Resource Manager, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Per informazioni introduttive su Archiviazione di Azure, vedere [Introduzione ad Archiviazione di Azure](../storage/storage-introduction.md).
* Per trovare altri runbook utili di Automazione di Azure, vedere [Raccolte di runbook e moduli per l'automazione di Azure](automation-runbook-gallery.md).
* Per trovare altri modelli utili di Resource Manager, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)


