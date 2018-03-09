---
title: Configurare l'ambiente di PowerShell dell'operatore dello Stack di Azure | Documenti Microsoft
description: Informazioni su come configurare l'ambiente di PowerShell dell'operatore dello Stack di Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: 57aa5a1ccc45548c3e789b50c888f669df39d5f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Configurare l'ambiente di PowerShell dell'operatore Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile configurare lo Stack di Azure per l'utilizzo di PowerShell per gestire le risorse, ad esempio la creazione di piani, le quote e avvisi. In questo argomento consente di configurare l'ambiente di operatore. Se si desidera configurare PowerShell per l'ambiente utente, vedere per [configurare l'ambiente di PowerShell dell'utente Azure Stack](user/azure-stack-powershell-configure-user.md) articolo.

## <a name="prerequisites"></a>Prerequisiti

Eseguire i seguenti prerequisiti dal [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows in caso di [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).  
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurare l'ambiente di operatore e accedere a Azure Stack

Configurare l'ambiente di operatore dello Stack di Azure con PowerShell. In base al tipo di distribuzione, Azure Active Directory o ADFS, eseguire uno degli script di seguito: sostituire il tenantName di Azure AD, endpoint GraphAudience e ArmEndpoint valori con la configurazione dell'ambiente.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Distribuzioni di Azure basato su Active Directory (Azure AD)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Distribuzioni basate su Active Directory Federation Services (ADFS)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che hai tutti gli elementi di configurazione, è possibile utilizzare PowerShell per creare le risorse all'interno dello Stack di Azure. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Utilizzare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi
* [Sviluppo di modelli per Azure Stack](user/azure-stack-develop-templates.md)
* [Distribuire modelli con PowerShell](user/azure-stack-deploy-template-powershell.md)
