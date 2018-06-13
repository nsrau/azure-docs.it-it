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
ms.openlocfilehash: 86608ef8b3623682bd10498605f8b7b62c377ff1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076471"
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
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
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
