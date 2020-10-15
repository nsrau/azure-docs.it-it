---
title: Distribuire un modello di Azure Resource Manager in un runbook PowerShell di Automazione di Azure
description: Questo articolo descrive come distribuire un modello di Azure Resource Manager archiviato in archiviazione di Azure da un Runbook di PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
keywords: powershell, runbook, json, automazione di azure
ms.openlocfilehash: 18f1d4ced2a80f9adb5da2c209987fc1997a3f22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304152"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Distribuire un modello di Azure Resource Manager in un runbook PowerShell

È possibile scrivere un [runbook PowerShell di Automazione di Azure](./learn/automation-tutorial-runbook-textual-powershell.md) che distribuisce una risorsa di Azure usando un [modello di Azure Resource Management](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). I modelli consentono di usare automazione di Azure per automatizzare la distribuzione delle risorse di Azure. È possibile anche gestire i modelli di Resource Manager in una posizione centrale protetta come Archiviazione di Azure.

In questo articolo si creerà un runbook PowerShell che usa un modello di Resource Manager archiviato in [Archiviazione di Azure](../storage/common/storage-introduction.md) per distribuire un nuovo account di Archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure [iscriversi per ottenere un account gratuito](https://azure.microsoft.com/free/).
* [Account di Automazione](./manage-runas-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* [Account di archiviazione di Azure](../storage/common/storage-account-create.md) in cui archiviare il modello di gestione risorse.
* Azure PowerShell installato in un computer locale. Per informazioni su come ottenere PowerShell, vedere [Come installare Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager

In questo esempio viene usato un modello di Gestione risorse che distribuisce un nuovo account di archiviazione di Azure.

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

Salvare il file localmente come **TemplateTest.JSON**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Salvare il modello di Resource Manager in Archiviazione di Azure

Si userà ora PowerShell per creare una condivisione file di Archiviazione di Azure e caricare il file **TemplateTest.json**. Per istruzioni su come creare una condivisione file e caricare un file nel portale di Azure, vedere [Introduzione ad Archiviazione file di Azure in Windows](../storage/files/storage-dotnet-how-to-use-files.md).

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

A questo punto viene creato uno script di PowerShell che ottiene il **TemplateTest.jsnel** file da archiviazione di Azure e distribuisce il modello per creare un nuovo account di archiviazione di Azure.

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

Salvare il file localmente come **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importare e pubblicare il runbook nell'account di Automazione di Azure

Si userà ora PowerShell per importare il runbook nell'account di Automazione di Azure e pubblicarlo. Per informazioni su come importare e pubblicare un runbook nel portale di Azure, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).

Per importare **DeployTemplate.ps1** nell'account di Automazione come un runbook di PowerShell, eseguire i comandi di PowerShell seguenti:

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

Si avvierà ora il runbook chiamando il cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Per informazioni su come avviare un runbook nel portale di Azure, vedere [Avvio di un Runbook in Automazione di Azure](./start-runbooks.md).

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

Dopo l'esecuzione di Runbook, è possibile controllarne lo stato recuperando il valore della proprietà dell'oggetto processo `$job.Status` .

Il runbook ottiene il modello di Resource Manager e lo usa per distribuire un nuovo account di Archiviazione di Azure. È possibile visualizzare il nuovo account di archiviazione creato eseguendo il comando seguente:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui modelli di Resource Manager, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Per informazioni introduttive su Archiviazione di Azure, vedere [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md).
* Per trovare altri runbook utili di Automazione di Azure, vedere [Usare runbook e moduli in Automazione di Azure](automation-runbook-gallery.md).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation#automation).
