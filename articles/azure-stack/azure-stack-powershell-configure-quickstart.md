---
title: Installare e configurare PowerShell per l'avvio rapido di Azure Stack | Documenti Microsoft
description: Informazioni sull'installazione e configurazione di PowerShell per lo Stack di Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: mabrigg
ms.openlocfilehash: ca61562607da274f0e0c7f504d1a24723210f2f7
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Diventare operativi con PowerShell nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Questa Guida rapida consente di installare e configurare un ambiente dello Stack di Azure con PowerShell. Lo script fornito in questo articolo ha come ambito il **operatore Azure Stack** solo.

In questo articolo è una versione ridotta dei passaggi descritti nel [installare PowerShell]( azure-stack-powershell-install.md), [scaricare strumenti]( azure-stack-powershell-download.md), e [configurare l'ambiente di PowerShell dell'operatore Azure Stack]( azure-stack-powershell-configure-admin.md) articoli. Utilizzando gli script in questo argomento, è possibile impostare per gli ambienti di Stack di Azure distribuiti con Azure Active Directory o Active Directory Federation Services (ADFS) di PowerShell.  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurare PowerShell per le distribuzioni basate su Azure Active Directory

Firmare il Kit di sviluppo dello Stack di Azure o un client esterno basato su Windows se si è connessi tramite VPN. Aprire una sessione di PowerShell ISE con privilegi elevata e quindi eseguire lo script seguente. Assicurarsi di aggiornare il **TenantName**, **ArmEndpoint**, e **GraphAudience** variabili necessarie per la configurazione dell'ambiente:

> [!IMPORTANT]
> La versione del modulo PowerShell di Azure Resource Manager 1.2.11 viene fornito con un elenco di modifiche di rilievo. Eseguire l'aggiornamento dal 1.2.10 versione, vedere il [Guida alla migrazione](https://aka.ms/azspowershellmigration).

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurare PowerShell per le distribuzioni di Active Directory basati su ADFS

Se si opera Stack Azure quando si è connessi a internet, è possibile utilizzare lo script seguente. Tuttavia se si opera Stack Azure senza connettività internet, utilizzare il [disconnesso modalità di installazione di PowerShell](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) e i cmdlet per configurare PowerShell rimarrà stessi, come illustrato nello script. Firmare il Kit di sviluppo dello Stack di Azure o un client esterno basato su Windows se si è connessi tramite VPN. Aprire una sessione di PowerShell ISE con privilegi elevata e quindi eseguire lo script seguente. Assicurarsi di aggiornare il **ArmEndpoint** e **GraphAudience** variabili necessarie per la configurazione dell'ambiente:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testare la connettività

Dopo aver configurato PowerShell, è possibile testare la configurazione tramite la creazione di un gruppo di risorse:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Dopo aver creato il gruppo di risorse, il **lo stato di Provisioning** è impostata su **Succeeded**.

## <a name="next-steps"></a>Passaggi successivi

* [Installare e configurare CLI](azure-stack-connect-cli.md)

* [Sviluppare modelli](user/azure-stack-develop-templates.md)







