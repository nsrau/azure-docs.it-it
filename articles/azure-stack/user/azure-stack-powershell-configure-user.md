---
title: Configurare l'ambiente di PowerShell dell'utente Azure Stack | Documenti Microsoft
description: Configurare l'ambiente di PowerShell dell'utente Azure Stack
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: 0bd5b4a98fee7a5d914e53e49a9517f5d3682a88
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Configurare l'ambiente di PowerShell dell'utente Azure Stack

Come un utente di Stack di Azure, è possibile configurare l'ambiente di PowerShell il Azure Stack Development Kit. Dopo aver configurato, è possibile utilizzare PowerShell per gestire le risorse, ad esempio effettuare la sottoscrizione, Stack di Azure creare macchine virtuali, distribuire modelli di gestione risorse di Azure e così via. L'ambito di questo argomento per l'utilizzo con l'utente ambienti, se si desidera configurare PowerShell per l'ambiente cloud operatore, fare riferimento solo a è il [configurare l'ambiente di PowerShell dell'operatore Azure Stack](../azure-stack-powershell-configure-admin.md) articolo. 

## <a name="prerequisites"></a>Prerequisiti 

Eseguire i seguenti prerequisiti dal [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows in caso di [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).  
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Configurare l'ambiente utente e accedere a Azure Stack

In base al tipo di distribuzione (Azure Active Directory o AD FS), eseguire uno degli script di seguito per configurare PowerShell per Azure Stack (assicurarsi di sostituire il tenantName AAD, GraphAudience endpoint e ArmEndpoint valori in base alla configurazione dell'ambiente):

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

Quando si opera su una sottoscrizione utente appena creato che non dispone di tutte le risorse distribuite tramite il portale, i provider di risorse non sono registrati automaticamente. È necessario registrarli in modo esplicito tramite lo script seguente:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che abbiamo tutti gli elementi di configurazione, è possibile utilizzare PowerShell per creare le risorse all'interno dello Stack di Azure. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Utilizzare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi
* [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
