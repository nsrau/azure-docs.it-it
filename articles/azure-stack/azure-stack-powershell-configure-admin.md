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
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 728f97773c2eec62c54235a868daca52dd6029a5
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731844"
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

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> ADFS supporta solo l'autenticazione interattiva con le identità degli utenti. Se è necessario un oggetto credenziale è necessario usare un'entità servizio (SPN). Per altre informazioni sulla configurazione di un'entità servizio con Azure Stack e AD FS come servizio di gestione di identità, vedere [Gestisci entità servizio per AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che hai tutti gli elementi di configurazione, usare PowerShell per creare risorse all'interno di Azure Stack. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Usare il comando seguente per creare un gruppo di risorse denominato **MyResourceGroup**.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi

 - [Sviluppo di modelli per Azure Stack](user/azure-stack-develop-templates.md)
 - [Distribuire modelli con PowerShell](user/azure-stack-deploy-template-powershell.md) <<<<<<< HEAD
 - [Riferimento al modulo PowerShell di Azure Stack](https://docs.microsoft.com/en-us/powershell/azure/azure-stack/overview)
=======
- [Riferimento al modulo di Azure Stack](https://docs.microsoft.com/en-us/powershell/azure/azure-stack/overview)  
>>>>>>> ac8c5a2d00d4eb5b4ef65359c5349ddff1bf30cf
