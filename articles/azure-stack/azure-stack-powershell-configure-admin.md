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
ms.date: 06/22/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 74a5a9408a78dd0da12fb3f8ed721774030cc438
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749862"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Configurare l'ambiente dello Stack di Azure PowerShell

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile configurare lo Stack di Azure per l'utilizzo di PowerShell per gestire le risorse, ad esempio la creazione di piani, quote e gli avvisi. Questo argomento consente di configurare l'ambiente di operatore.

## <a name="prerequisites"></a>Prerequisiti

Eseguire i seguenti prerequisiti tramite il [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows se si è [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Installare [i moduli di PowerShell Azure compatibile con Stack Azure](azure-stack-powershell-install.md).  
 - Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurare l'ambiente di operatore e accedere a Azure Stack

Configurare l'ambiente di operatore dello Stack di Azure con PowerShell. Eseguire uno degli script di seguito: sostituzione tenantName Azure AD, GraphAudience endpoint e ArmEndpoint valori con la propria configurazione dell'ambiente.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che hai tutto questo caso, utilizzare PowerShell per creare le risorse all'interno dello Stack di Azure. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Utilizzare il comando seguente per creare un gruppo di risorse denominato **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi

 - [Sviluppo di modelli per Azure Stack](user/azure-stack-develop-templates.md)
 - [Distribuire modelli con PowerShell](user/azure-stack-deploy-template-powershell.md)
