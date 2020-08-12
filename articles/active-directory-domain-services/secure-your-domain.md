---
title: Azure AD Domain Services protetto | Microsoft Docs
description: Informazioni su come disabilitare le crittografie vulnerabili, i protocolli obsoleti e la sincronizzazione degli hash delle password NTLM per un dominio gestito Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 50cf58f83115cfb8c84fe7b2a37b6664c2d9c567
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88116683"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Disabilitare le crittografie vulnerabili e la sincronizzazione degli hash delle password per proteggere un dominio gestito Azure Active Directory Domain Services

Per impostazione predefinita, Azure Active Directory Domain Services (Azure AD DS) consente l'utilizzo di crittografie come NTLM v1 e TLS v1. Queste crittografie potrebbero essere necessarie per alcune applicazioni legacy, ma sono considerate vulnerabili e possono essere disabilitate se non sono necessarie. Se si dispone di connettività ibrida locale con Azure AD Connect, è anche possibile disabilitare la sincronizzazione degli hash delle password NTLM.

Questo articolo illustra come disabilitare le crittografie NTLM v1 e TLS v1 e disabilitare la sincronizzazione degli hash delle password NTLM.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessarie le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un dominio gestito di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Installare e configurare Azure PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](/powershell/azure/install-az-ps).
    * Assicurarsi di accedere alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount][Connect-AzAccount].
* Installare e configurare Azure AD PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD](/powershell/azure/active-directory/install-adv2).
    * Assicurarsi di accedere al tenant di Azure usando il cmdlet [Connect-AzureAD][Connect-AzureAD].

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

Infine, applicare le impostazioni di sicurezza definite al dominio gestito usando il cmdlet [set-AzResource][Set-AzResource] . Specificare la risorsa Azure AD DS dal primo passaggio e le impostazioni di sicurezza del passaggio precedente.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Sono necessari alcuni istanti per applicare le impostazioni di sicurezza al dominio gestito.

> [!IMPORTANT]
> Dopo aver disattivato NTLM, eseguire una sincronizzazione completa dell'hash delle password in Azure AD Connect per rimuovere tutti gli hash delle password dal dominio gestito. Se si disattiva NTLM ma non si impone la sincronizzazione degli hash delle password, gli hash delle password NTLM per un account utente vengono rimossi solo alla successiva modifica della password. Questo comportamento potrebbe consentire a un utente di continuare ad accedere se dispone di credenziali memorizzate nella cache in un sistema in cui NTLM viene usato come metodo di autenticazione.
>
> Una volta che l'hash della password NTLM è diverso dall'hash della password Kerberos, il fallback a NTLM non funzionerà. Le credenziali memorizzate nella cache non funzionano più anche se la macchina virtuale dispone di connettività al controller di dominio gestito.  

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul processo di sincronizzazione, vedere l'articolo relativo alla sincronizzazione [di oggetti e credenziali in un dominio gestito][synchronization].

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
