---
title: Configurare l'ambiente Azure Stack PowerShell | Documenti Microsoft
description: Informazioni su come configurare l'ambiente dello Stack di Azure PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 48a765b4e613e45699099330e4ff5c3ce7512686
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300738"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Configurare l'ambiente dello Stack di Azure PowerShell

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile configurare lo Stack di Azure per l'utilizzo di PowerShell per gestire le risorse, ad esempio la creazione di piani, le quote e avvisi. In questo argomento consente di configurare l'ambiente di operatore.

## <a name="prerequisites"></a>Prerequisiti

Eseguire i seguenti prerequisiti dal [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows in caso di [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

 - Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).  
 - Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurare l'ambiente di operatore e accedere a Azure Stack

Configurare l'ambiente di operatore dello Stack di Azure con PowerShell. In base al tipo di distribuzione, Azure Active Directory o ADFS, eseguire uno degli script di seguito: sostituire il tenantName di Azure AD, endpoint GraphAudience e ArmEndpoint valori con la configurazione dell'ambiente.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Distribuzioni di Azure basato su Active Directory (Azure AD)

````PowerShell  
# Set the Azure Stack Tools location
$AzSTools = "C:\AzureStack-Tools-master"

# Set the Azure Active Directory Tenant Name that is used to deploy Azure Stack
$AadTenantName = "<myDirectoryTenantName>.onmicrosoft.com"

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
# To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/.
# To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<Graph Audience endpoint for your environment>"

# Navigate to the Azure Stack Tools folder and import the **Connect** PowerShell module
cd $AzSTools
Set-ExecutionPolicy RemoteSigned
Import-Module .\Connect\AzureStack.Connect.psm1

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

# Get the Active Directory tenantId that is used to deploy Azure Stack
$TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $AadTenantName `
    -EnvironmentName "AzureStackAdmin"

# After signing in to your environment, Azure Stack cmdlets
# can be easily targeted at your Azure Stack instance.
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Distribuzioni basate su Active Directory Federation Services (ADFS)

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che hai tutti gli elementi di configurazione, è possibile utilizzare PowerShell per creare le risorse all'interno dello Stack di Azure. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Utilizzare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi
 - [Sviluppo di modelli per Azure Stack](user/azure-stack-develop-templates.md)
 - [Distribuire modelli con PowerShell](user/azure-stack-deploy-template-powershell.md)
