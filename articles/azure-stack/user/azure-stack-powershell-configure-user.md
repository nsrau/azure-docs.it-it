---
title: Configurare l'ambiente di PowerShell dell'utente Azure Stack | Documenti Microsoft
description: Configurare l'ambiente di PowerShell dell'utente Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Configurare l'ambiente di PowerShell dell'utente Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare le istruzioni in questo articolo per configurare l'ambiente di PowerShell per un utente di Azure Stack.
Dopo aver configurato l'ambiente, è possibile utilizzare PowerShell per gestire le risorse di Azure Stack. Ad esempio, è possibile utilizzare PowerShell per effettuare la sottoscrizione, creare le macchine virtuali e distribuire modelli di gestione risorse di Azure.

>[!NOTE]
>In questo articolo è l'ambito per gli ambienti utente dello Stack di Azure. Se si desidera configurare PowerShell per l'ambiente di operatore cloud, vedere la [configurare l'ambiente di PowerShell dell'operatore Azure Stack](../azure-stack-powershell-configure-admin.md) articolo.

## <a name="prerequisites"></a>Prerequisiti

È possibile configurare questi prerequisiti dal [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows se sono [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Configurare l'ambiente utente e accedere a Azure Stack

In base al tipo di distribuzione dello Stack di Azure (Azure Active Directory o AD FS), eseguire uno degli script di seguito per configurare PowerShell per Azure Stack.

Assicurarsi di sostituire le seguenti variabili di script con i valori dalla configurazione dello Stack di Azure:

* TenantName AAD
* Endpoint GraphAudience
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>Distribuzioni basate su Azure Active Directory (AAD)

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Distribuzioni basate su Active Directory Federation Services (ADFS)

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>Registrare i provider di risorse

I provider di risorse non sono registrati automaticamente per le nuove sottoscrizioni utente che non dispongono di tutte le risorse distribuite tramite il portale. È possibile registrare un provider di risorse in modo esplicito eseguendo lo script seguente:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testare la connettività

Quando è pronto impostato, testare la connettività tramite PowerShell per creare risorse nello Stack di Azure. Come un test, creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Eseguire il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi

* [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
