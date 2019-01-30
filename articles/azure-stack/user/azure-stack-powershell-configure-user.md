---
title: Connettersi ad Azure Stack con PowerShell come utente | Microsoft Docs
description: Passaggi per connettersi all'istanza di Azure Stack dell'utente.
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
ms.date: 01/24/2019
ms.author: mabrigg
ms.reviewer: bganapa
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 450628019d705584c28acb86be214a478eb146a4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244046"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Connettersi ad Azure Stack con PowerShell come utente

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questo articolo offre i passaggi per connettersi all'istanza di Azure Stack. È necessario connettersi per gestire le risorse di Azure Stack con PowerShell. Ad esempio, è possibile usare PowerShell per effettuare la sottoscrizione, creare macchine virtuali e distribuire modelli di Azure Resource Manager. Per eseguire i cmdlet di PowerShell.

Per configurare:
  - Assicurarsi di avere i requisiti.
  - Connessione con Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS). 
  - Registrare i provider di risorse.
  - Testare la connettività.

## <a name="prerequisites"></a>Prerequisiti

È possibile configurare questi prerequisiti dal [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows se sei [connesse tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installare [moduli di Azure Stack-compatibili con Azure PowerShell](azure-stack-powershell-install.md).
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).

Assicurarsi di sostituire le variabili dello script seguente con i valori dalla configurazione di Azure Stack:

- **Nome del tenant Azure AD**  
  Il nome del tenant di Azure AD consente di gestire Azure Stack, ad esempio, yourdirectory.onmicrosoft.com.
- **Endpoint di Azure Resource Manager**  
  Per il kit di sviluppo di Azure Stack, questo valore è impostato su https://management.local.azurestack.external. Per ottenere questo valore per i sistemi integrati di Azure Stack, contattare il provider di servizi.

## <a name="connect-with-azure-ad"></a>Connettersi con Azure AD

```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Connect con AD FS

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -Credential $cred
  ```

## <a name="register-resource-providers"></a>Registrare i provider di risorse

I provider di risorse non vengono registrati automaticamente per le nuove sottoscrizioni utente che non sono presenti eventuali risorse distribuite tramite il portale. È possibile registrare un provider di risorse in modo esplicito eseguendo lo script seguente:

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testare la connettività

Dopo aver ottenuto tutte le impostazioni, testare la connettività dall'uso di PowerShell per creare le risorse in Azure Stack. Come un test, creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Eseguire il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi

- [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
- [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
- Se si desidera configurare PowerShell per l'ambiente di operatore cloud, vedere la [configurare l'ambiente PowerShell dell'operatore Azure Stack](../azure-stack-powershell-configure-admin.md) articolo.
