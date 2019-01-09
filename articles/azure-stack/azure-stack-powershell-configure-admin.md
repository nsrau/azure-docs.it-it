---
title: Connettersi ad Azure Stack con PowerShell come un operatore | Microsoft Docs
description: Informazioni su come connettersi ad Azure Stack con PowerShell come un operatore
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
ms.date: 12/07/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 7a744520fe2a3b53b1306d4c80a5eca7d86258a7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104540"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Connettersi ad Azure Stack con PowerShell come un operatore

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile configurare lo Stack di Azure per usare PowerShell per gestire le risorse, ad esempio la creazione di offerte, piani, le quote e avvisi. Questo argomento consente di configurare l'ambiente di operatore.

## <a name="prerequisites"></a>Prerequisiti

Eseguire i seguenti prerequisiti dal [kit di sviluppo](./asdk/asdk-connect.md#connect-with-rdp) o da un client esterno con codifica basata su Windows se sei [connesso al ASDK tramite VPN](./asdk/asdk-connect.md#connect-with-vpn). 

 - Installare [moduli di Azure Stack-compatibili con Azure PowerShell](azure-stack-powershell-install.md).  
 - Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Connettersi con Azure AD

Configurare l'ambiente di operatore di Azure Stack con PowerShell. Eseguire uno degli script seguenti: Sostituire il Nometenant Azure Active Directory (Azure AD) e valori dell'endpoint Azure Resource Manager con la propria configurazione di ambiente. <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Connect con AD FS

Connettersi all'ambiente di operatore di Azure Stack con PowerShell con Azure Active Directory Federated Services (Azure AD FS). Per il kit di sviluppo di Azure Stack, questo endpoint di Azure Resource Manager è impostato su `https://adminmanagement.local.azurestack.external`. Per ottenere l'endpoint Azure Resource Manager per i sistemi integrati di Azure Stack, contattare il provider di servizi.

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $tenantId `
    -Credential $cred
  ```



## <a name="test-the-connectivity"></a>Testare la connettività

Ora che hai tutti gli elementi di configurazione, usare PowerShell per creare risorse all'interno di Azure Stack. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Usare il comando seguente per creare un gruppo di risorse denominato **MyResourceGroup**.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi

 - [Sviluppo di modelli per Azure Stack](user/azure-stack-develop-templates.md)
 - [Distribuire modelli con PowerShell](user/azure-stack-deploy-template-powershell.md)
