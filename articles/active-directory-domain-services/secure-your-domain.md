---
title: Azure AD Domain Services protetto | Microsoft Docs '
description: Informazioni su come disabilitare le crittografie vulnerabili, i protocolli obsoleti e la sincronizzazione degli hash delle password NTLM per un dominio gestito Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: db086c56c9f16f4691efaade03571bf8a36c6444
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842618"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Disabilitare le crittografie vulnerabili e la sincronizzazione degli hash delle password per proteggere un dominio gestito Azure AD Domain Services

Per impostazione predefinita, Azure Active Directory Domain Services (Azure AD DS) consente l'utilizzo di crittografie come NTLM v1 e TLS v1. Queste crittografie potrebbero essere necessarie per alcune applicazioni legacy, ma sono considerate vulnerabili e possono essere disabilitate se non sono necessarie. Se si dispone di connettività ibrida locale con Azure AD Connect, è anche possibile disabilitare la sincronizzazione degli hash delle password NTLM.

Questo articolo illustra come disabilitare le crittografie NTLM v1 e TLS v1 e disabilitare la sincronizzazione degli hash delle password NTLM.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessarie le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Installare e configurare Azure PowerShell.
    * Se necessario, seguire le istruzioni per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](/powershell/azure/install-az-ps).
    * Assicurarsi di accedere alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Installare e configurare Azure AD PowerShell.
    * Se necessario, seguire le istruzioni per [installare il modulo Azure ad PowerShell e connettersi al Azure ad](/powershell/azure/active-directory/install-adv2).
    * Assicurarsi di accedere al tenant di Azure AD usando il cmdlet [Connect-AzureAD][Connect-AzureAD] .

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Disabilitare le crittografie vulnerabili e la sincronizzazione degli hash delle password NTLM

Per disabilitare i pacchetti di crittografia vulnerabili e la sincronizzazione degli hash delle credenziali NTLM, accedere al proprio account Azure e quindi ottenere la risorsa Azure AD DS usando il cmdlet [Get-AzResource][Get-AzResource] :

> [!TIP]
> Se si riceve un errore usando il comando [Get-AzResource][Get-AzResource] che la risorsa *Microsoft. AAD/DomainServices* non esiste, [elevare l'accesso per gestire tutti i gruppi di gestione e le sottoscrizioni di Azure][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definire quindi *DomainSecuritySettings* per configurare le opzioni di sicurezza seguenti:

1. Disabilitare il supporto di NTLM v1.
2. Disabilitare la sincronizzazione degli hash delle password NTLM dall'istanza di Active Directory locale.
3. Disabilitare TLS v1.

> [!IMPORTANT]
> Gli account utente e del servizio non possono eseguire binding LDAP semplici se si disabilita la sincronizzazione dell'hash delle password NTLM nel dominio gestito di Azure AD DS. Se è necessario eseguire binding semplici LDAP, non impostare l'opzione di configurazione *"SyncNtlmPasswords" = "disabled";* Security nel comando seguente.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Infine, applicare le impostazioni di sicurezza definite al dominio gestito di Azure AD DS usando il cmdlet [set-AzResource][Set-AzResource] . Specificare la risorsa Azure AD DS dal primo passaggio e le impostazioni di sicurezza del passaggio precedente.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Per applicare le impostazioni di sicurezza al dominio gestito di Azure AD DS sono necessari alcuni istanti.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul processo di sincronizzazione, vedere la sincronizzazione [di oggetti e credenziali in un dominio gestito Azure AD DS][synchronization].

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