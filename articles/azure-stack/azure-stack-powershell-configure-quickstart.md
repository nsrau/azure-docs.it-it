---
title: Installare e configurare PowerShell per l'avvio rapido di Azure Stack | Microsoft Docs
description: Informazioni sull'installazione e configurazione di PowerShell per Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5d988e8a8a32924b8424a07cf20c75f0e8f8cf4d
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391075"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Ottieni operatività con PowerShell in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questa Guida introduttiva illustra come installare e configurare un ambiente Azure Stack con PowerShell. Lo script che viene fornito in questo articolo ha come ambito il **operatore di Azure Stack** solo.

Questo articolo è una versione ridotta dei passaggi descritti nel [installare PowerShell]( azure-stack-powershell-install.md), [scaricare strumenti]( azure-stack-powershell-download.md), e [configurare l'ambiente di PowerShell dell'operatore Azure Stack]( azure-stack-powershell-configure-admin.md) articoli. Usando gli script in questo articolo, è possibile configurare PowerShell per gli ambienti Azure Stack che vengono distribuiti con Azure Active Directory o Active Directory Federation Services (ADFS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurare PowerShell per le distribuzioni basate su Azure Active Directory

<a name="sign-in-to-your-azure-stack-development-kit-or-a-windows-based-external-client-if-you-are-connected-through-vpn-open-an-elevated-powershell-ise-session-and-then-run-the-following-script"></a>Accedi per il database di Azure Stack Development Kit, o un client esterno basato su Windows se si è connessi tramite VPN. Aprire una sessione di PowerShell ISE con privilegi elevata e quindi eseguire lo script seguente. 
-  
- Assicurarsi di aggiornare il **Nometenant**, **ArmEndpoint**, e **GraphAudience** variabili in base alle esigenze per la configurazione di ambiente:

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
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
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

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurare PowerShell per le distribuzioni basate su ADFS di Active Directory

Se si opera Azure Stack quando si è connessi a internet, è possibile usare lo script seguente. Tuttavia se si opera dello Stack di Azure senza connettività internet, usare il [disconnesso modalità di installazione di PowerShell](azure-stack-powershell-install.md) e i cmdlet per configurare PowerShell rimarranno stessi come illustrato in questo script. Accedi per il database di Azure Stack Development Kit, o un client esterno basato su Windows se si è connessi tramite VPN. Aprire una sessione di PowerShell ISE con privilegi elevata e quindi eseguire lo script seguente. Assicurarsi di aggiornare il **ArmEndpoint** e **GraphAudience** variabili in base alle esigenze per la configurazione di ambiente:

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
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
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

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che è stato configurato PowerShell, è possibile testare la configurazione tramite la creazione di un gruppo di risorse:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Per specificare un gruppo di risorse, è necessario disporre di un gruppo di risorse nella sottoscrizione. Per altre informazioni sulle sottoscrizioni, vedere [Cenni preliminari sul piano, offerta, quote e sottoscrizione](azure-stack-plan-offer-quota-overview.md)

Dopo aver creato il gruppo di risorse, il **lo stato di Provisioning** è impostata su **Succeeded**.

## <a name="next-steps"></a>Passaggi successivi

* [Installare e configurare CLI](azure-stack-connect-cli.md)

* [Sviluppare modelli](user/azure-stack-develop-templates.md)
