---
title: Abilitare multi-tenancy nello Stack di Azure | Documenti Microsoft
description: "Informazioni su come supportare più directory di Azure Active Directory nello Stack di Azure"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: a2cba85a553f20040d2fb118b35859b05870e361
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Abilitare multi-tenancy nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile configurare dello Stack di Azure per supportare gli utenti da più tenant di Azure Active Directory (Azure AD) per utilizzare i servizi nello Stack di Azure. Ad esempio, si consideri lo scenario seguente:

 - Si è amministratore del servizio di contoso.onmicrosoft.com, Stack di Azure in cui è installato.
 - Mary è l'amministratore di Directory di fabrikam.onmicrosoft.com, in cui si trovano gli utenti guest. 
 - Società Mary riceve servizi IaaS e PaaS dall'azienda e deve consentire agli utenti di accedere e utilizzare le risorse di Azure Stack contoso.onmicrosoft.com directory guest (fabrikam.onmicrosoft.com).

Questa guida fornisce i passaggi necessari per configurare multi-tenancy nello Stack di Azure nel contesto di questo scenario.  In questo scenario, si e Mary necessario completare i passaggi per consentire agli utenti da Fabrikam per accedere ai servizi e della distribuzione di Azure Stack in Contoso.  

## <a name="before-you-begin"></a>Prima di iniziare
Esistono alcuni prerequisiti per tenere conto prima di configurare multi-tenancy nello Stack di Azure:
  
 - L'utente e Mary deve coordinare alcuni passaggi amministrativi in directory di che installazione dello Stack di Azure (Contoso) e la directory guest (Fabrikam).  
 - Assicurarsi di aver [installato](azure-stack-powershell-install.md) e [configurato](azure-stack-powershell-configure-admin.md) PowerShell per Azure Stack.
 - [Scaricare gli strumenti di Azure Stack](azure-stack-powershell-download.md)e importare i moduli di connessione e l'identità:

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary richiederà [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) accesso allo Stack di Azure. 

## <a name="configure-azure-stack-directory"></a>Configurare la directory Azure Stack
In questa sezione è configurare dello Stack di Azure per consentire gli accessi dai tenant di directory di Azure AD di Fabrikam.

### <a name="onboard-guest-directory-tenant"></a>Tenant di directory onboard guest
Quindi, caricare il Tenant di Directory Guest (Fabrikam) allo Stack di Azure.  Questo passaggio consente di configurare Gestione risorse di Azure per accettare utenti ed entità servizio dal tenant di directory guest.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local"
````



## <a name="configure-guest-directory"></a>Configurare la directory guest
Dopo aver completato i passaggi nella directory di Azure Stack, Mary deve fornire il consenso allo Stack di Azure accede alla directory guest e registrare dello Stack di Azure con la directory di guest. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrazione dello Stack di Azure con la directory di guest
Dopo che l'amministratore di directory guest ha fornito il consenso per lo Stack di Azure accedere alla directory di Fabrikam, è necessario registrare Azure Stack con tenant di directory di Fabrikam.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Indicare agli utenti di accedere
Ora che si e Maria sono stati completati i passaggi relativi alla directory di Mary onboard, Mary possano indirizzare gli utenti di Fabrikam per effettuare l'accesso.  Gli utenti di Fabrikam (vale a dire, gli utenti con il suffisso fabrikam.onmicrosoft.com) accedono visitando https://portal.local.azurestack.external.  

Mary indirizzerà qualsiasi [entità esterna](../active-directory/active-directory-understanding-resource-access.md) nella directory di Fabrikam (vale a dire, gli utenti nella directory senza il suffisso del fabrikam.onmicrosoft.com Fabrikam) per accedere tramite https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Se non utilizzano questo URL, vengono inviati alla directory predefinita (Fabrikam) e a ricevere un messaggio di errore che all'amministratore non ha accettato le condizioni.

## <a name="next-steps"></a>Fasi successive

- [Gestire i provider di delegati](azure-stack-delegated-provider.md)
- [Concetti chiave di Azure Stack](azure-stack-key-features.md)
