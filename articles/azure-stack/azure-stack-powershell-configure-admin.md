---
title: Configurare l'ambiente di PowerShell dell'operatore dello Stack di Azure | Documenti Microsoft
description: Informazioni su come configurare l'ambiente di PowerShell dell'operatore dello Stack di Azure.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: sngun
ms.openlocfilehash: 54f0305afb94b8b19ea7fada837a73f7c6a1963c
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Configurare l'ambiente di PowerShell dell'operatore Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come operatore di Stack di Azure, è possibile configurare l'ambiente di PowerShell il Azure Stack Development Kit. Dopo aver configurato, è possibile utilizzare PowerShell per gestire le risorse di Azure Stack, ad esempio la creazione di offerte, i piani, le quote di gestione di avvisi e così via. Ambito di questo argomento per l'utilizzo con l'operatore cloud ambienti, se si desidera configurare PowerShell per l'ambiente utente, fare riferimento solo a [configurare l'ambiente di PowerShell dell'utente Azure Stack](user/azure-stack-powershell-configure-user.md) argomento. 

## <a name="prerequisites"></a>Prerequisiti

Eseguire i seguenti prerequisiti dal [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows in caso di [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).  
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurare l'ambiente di operatore e accedere a Azure Stack

In base al tipo di distribuzione (Azure Active Directory o AD FS), eseguire uno dello script seguente per configurare l'ambiente di operatore dello Stack di Azure con PowerShell (assicurarsi di sostituire il tenantName AAD, GraphAudience endpoint e ArmEndpoint valori in base al proprio ambiente configurazione):

### <a name="azure-active-directory-aad-based-deployments"></a>Distribuzioni basate su Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience 

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Distribuzioni basate su Active Directory Federation Services (ADFS)
         
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint


  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

## <a name="test-the-connectivity"></a>Testare la connettività

Ora che abbiamo tutto ciò che è impostato, è possibile utilizzare PowerShell per creare risorse all'interno dello Stack di Azure. Ad esempio, è possibile creare un gruppo di risorse per un'applicazione e aggiungere una macchina virtuale. Utilizzare il comando seguente per creare un gruppo di risorse denominato "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passaggi successivi
* [Sviluppo di modelli per Azure Stack](user/azure-stack-develop-templates.md)
* [Distribuire modelli con PowerShell](user/azure-stack-deploy-template-powershell.md)
