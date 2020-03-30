---
title: Servizi di dominio di Azure AD sicuri Documenti Microsoft
description: Informazioni su come disabilitare le crittografie deboli, i protocolli precedenti e la sincronizzazione dell'hash delle password NTLM per un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to disable weak ciphers, old protocols, and NTLM password hash synchronization for an Azure Active Directory Domain Services managed domain.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 8eee516beaaf26ed25bd20f9689d26fdb1eb9b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546230"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Disabilitare la sincronizzazione di crittografia debole e hash delle password per proteggere un dominio gestito di Servizi di dominio Azure ADDisable weak ciphers and password hash synchronization to secure an Azure AD Domain Services managed domain

Per impostazione predefinita, Servizi di dominio Azure Active Directory (Azure AD DS) consente l'uso di crittografia, ad esempio NTLM v1 e TLS v1. Queste crittografie possono essere necessarie per alcune applicazioni legacy, ma sono considerate deboli e possono essere disabilitate se non sono necessarie. Se si dispone di connettività ibrida locale con Azure AD Connect, è anche possibile disabilitare la sincronizzazione degli hash delle password NTLM.

In questo articolo viene illustrato come disattivare la crittografia NTLM v1 e TLS v1 e disabilitare la sincronizzazione dell'hash delle password NTLM.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessarie le risorse seguenti:To complete this article, you need the following resources:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Installare e configurare Azure PowerShell.
    * Se necessario, seguire le istruzioni per [installare il modulo di Azure PowerShell e connettersi alla sottoscrizione](/powershell/azure/install-az-ps)di Azure.
    * Assicurarsi di accedere alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Installare e configurare Azure AD PowerShell.Install and configure Azure AD PowerShell.
    * Se necessario, seguire le istruzioni per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD.](/powershell/azure/active-directory/install-adv2)
    * Assicurarsi di accedere al tenant di Azure AD utilizzando il cmdlet [Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Disabilitare la sincronizzazione dell'hash delle password deboli e dell'hash delle password NTLM

Per disabilitare le suite di crittografia deboli e la sincronizzazione dell'hash delle credenziali NTLM, accedere all'account Azure, quindi ottenere la risorsa di Azure AD DS usando il cmdlet [Get-AzResource:To][Get-AzResource] disable weak cipher suites and NTLM credential hash synchronization, sign in to your Azure account, then get the Azure AD DS resource using the Get-AzResource cmdlet:

> [!TIP]
> Se viene visualizzato un errore utilizzando il comando [Get-AzResource][Get-AzResource] che la risorsa *Microsoft.AAD/DomainServices* non esiste, [elevare l'accesso per gestire tutte le sottoscrizioni e i gruppi][global-admin]di gestione di Azure.

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definire quindi DomainSecuritySettings per configurare le opzioni di sicurezza seguenti:Next, define *DomainSecuritySettings* to configure the following security options:

1. Disabilitare il supporto NTLM v1.
2. Disabilitare la sincronizzazione degli hash delle password NTLM dall'istanza di Active Directory locale.
3. Disabilitare TLS v1.

> [!IMPORTANT]
> Gli utenti e gli account di servizio non possono eseguire binding semplici LDAP se si disabilita la sincronizzazione dell'hash delle password NTLM nel dominio gestito di Azure AD DS. Se è necessario eseguire associazioni semplici LDAP, non impostare l'opzione di configurazione della protezione *"SyncNtlmPasswords"; "Disabled";*

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Infine, applicare le impostazioni di sicurezza definite al dominio gestito di Servizi di dominio Active Directory di Azure usando il cmdlet Set-AzResource.Finally, apply the defined security settings to the Azure AD DS managed domain using the [Set-AzResource][Set-AzResource] cmdlet. Specificare la risorsa Di SDi Azure AD dal primo passaggio e le impostazioni di sicurezza del passaggio precedente.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

L'applicazione delle impostazioni di sicurezza al dominio gestito di Servizi di dominio Active Directory di Azure richiede alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul processo di sincronizzazione, vedere Modalità di sincronizzazione di oggetti e credenziali in un dominio gestito di Servizi di dominio Active Directory di Azure.To learn more about the synchronization process, see [How objects and credentials are synchronized in an Azure AD DS managed domain][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD