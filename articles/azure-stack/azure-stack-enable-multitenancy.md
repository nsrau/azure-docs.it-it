---
title: Multi-tenancy in Azure Stack
description: Informazioni su come supportare più directory di Azure Active Directory in Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: patricka
ms.openlocfilehash: e61b4457cd88c236145ce7595ee7db4340538465
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331140"
---
# <a name="multi-tenancy-in-azure-stack"></a>Multi-tenancy in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile configurare Azure Stack per supportare gli utenti da più tenant di Azure Active Directory (Azure AD) per usare i servizi in Azure Stack. Ad esempio, si consideri lo scenario seguente:

 - Sei l'amministratore del servizio di contoso.onmicrosoft.com, in cui è installato Azure Stack.
 - Mary è l'amministratore di Directory di fabrikam.onmicrosoft.com, in cui si trovano gli utenti guest. 
 - La società di utente di Mary riceve servizi IaaS e PaaS dall'azienda e deve consentire agli utenti dalla directory guest (fabrikam.onmicrosoft.com) accedere e usare le risorse di Azure Stack in contoso.onmicrosoft.com.

Questa guida fornisce i passaggi necessari per configurare multi-tenancy in Azure Stack nel contesto di questo scenario. In questo scenario, è necessario completare i passaggi per abilitare gli utenti da Fabrikam per accedere e utilizzare i servizi dalla distribuzione con Azure Stack in Contoso si e Mary.  

## <a name="enable-multi-tenancy"></a>Abilitare il multi-tenancy

Esistono alcuni prerequisiti per tenere conto prima di configurare multi-tenancy in Azure Stack:
  
 - Sia Mary devono coordinare alcuni passaggi amministrativi tra la directory di che Azure Stack è installato in (Contoso) e la directory guest (Fabrikam).  
 - Assicurarsi di aver [installata](azure-stack-powershell-install.md) e [configurato](azure-stack-powershell-configure-admin.md) PowerShell per Azure Stack.
 - [Scaricare gli strumenti di Azure Stack](azure-stack-powershell-download.md)e importare i moduli Connect e l'identità:

    ````PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ````

 - Mary richiederanno [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) accesso ad Azure Stack. 

### <a name="configure-azure-stack-directory"></a>Configura directory di Azure Stack

In questa sezione si configura Azure Stack per consentire gli accessi dal tenant di directory Fabrikam di Azure AD.

Eseguire l'onboarding nel Tenant di Directory Guest (Fabrikam) per Azure Stack tramite la configurazione di Azure Resource Manager per accettare gli utenti ed entità dal tenant di directory guest servizio.

````PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
````

### <a name="configure-guest-directory"></a>Configurare la directory guest

Dopo aver completato i passaggi nella directory di Azure Stack, Mary è necessario fornire il consenso ad accedere alla directory guest di Azure Stack e registrare Azure Stack con la directory guest. 

#### <a name="registering-azure-stack-with-the-guest-directory"></a>La registrazione di Azure Stack con la directory guest

Una volta che l'amministratore di directory guest ha fornito il consenso per Azure Stack accedere alla directory di Fabrikam, Mary è necessario registrare Azure Stack con il tenant di directory di Fabrikam.

````PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````

> [!IMPORTANT]
> Se l'amministratore di Azure Stack consente di installare nuovi servizi o aggiornamenti in futuro, si potrebbe essere necessario eseguire nuovamente lo script.
>
> Eseguire questo script in qualsiasi momento per verificare lo stato delle applicazioni nella directory di Azure Stack.

### <a name="direct-users-to-sign-in"></a>Indirizzare gli utenti per l'accesso

Ora che si e Mary stati completati i passaggi per la directory di utente di Mary l'onboarding, Mary può indirizzare Fabrikam agli utenti di accedere.  Gli utenti di Fabrikam (vale a dire, gli utenti con il suffisso fabrikam.onmicrosoft.com) accedere visitando https://portal.local.azurestack.external.  

Mary indirizzerà eventuali [entità esterne](../role-based-access-control/rbac-and-directory-admin-roles.md) nella directory di Fabrikam (vale a dire, gli utenti nella directory di Fabrikam senza il suffisso di fabrikam.onmicrosoft.com) di accedere usando https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Se non usano questo URL, essi vengono inviati alla directory predefinita (Fabrikam) e visualizzato un errore indicante che l'amministratore non ha acconsentito.

## <a name="disable-multi-tenancy"></a>Disabilitare multi-tenancy

Se non si desidera più più tenant in Azure Stack, è possibile disabilitare multi-tenancy effettuando le operazioni seguenti nell'ordine:

1. Come amministratore della directory guest (Mary in questo scenario), eseguire *Unregister-AzsWithMyDirectoryTenant*. Il cmdlet consente di disinstallare tutte le applicazioni di Azure Stack nella nuova directory.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Come amministratore del servizio di Azure Stack (in questo scenario si), eseguire *Unregister-AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manaager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > I passaggi di multi-tenancy disable devono essere eseguiti in ordine. Passaggio #1 ha esito negativo se viene prima completato passaggio #2.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire i provider delegati](azure-stack-delegated-provider.md)
- [Concetti chiave di Azure Stack](azure-stack-key-features.md)
