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
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: f5df27df5901d6568b30e1ae4f40cae8b3de4c86
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298820"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Connettersi ad Azure Stack con PowerShell come un operatore

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile configurare lo Stack di Azure per usare PowerShell per gestire le risorse, ad esempio la creazione di offerte, piani, le quote e avvisi. Questo argomento consente di configurare l'ambiente di operatore.

## <a name="prerequisites"></a>Prerequisiti

Eseguire i seguenti prerequisiti dal [kit di sviluppo](.\asdk\asdk-connect.md#connect-with-rdp) o da un client esterno con codifica basata su Windows se sei [connesso al ASDK tramite VPN](.\asdk\asdk-connect.md#connect-with-vpn). 

 - Installare [moduli di Azure Stack-compatibili con Azure PowerShell](azure-stack-powershell-install.md).  
 - Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurare l'ambiente di operatore e accedere ad Azure Stack

Configurare l'ambiente di operatore di Azure Stack con PowerShell. Eseguire uno dei seguenti script: sostituire Nometenant di Azure AD, GraphAudience endpoint e ArmEndpoint valori con la propria configurazione di ambiente.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRmEnvironment -Name "AzureStackAdmin" -ARMEndpoint $ArmEndpoint -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external


    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $tenantId
````

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che hai tutti gli elementi di configurazione, usare PowerShell per creare risorse all'interno di Azure Stack. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Usare il comando seguente per creare un gruppo di risorse denominato **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi

 - [Sviluppo di modelli per Azure Stack](user/azure-stack-develop-templates.md)
 - [Distribuire modelli con PowerShell](user/azure-stack-deploy-template-powershell.md)