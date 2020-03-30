---
title: Risolvere i problemi di accesso in Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come risolvere i problemi e gli errori comuni di accesso degli utenti in Servizi di dominio Azure Active Directory.Learn how to troubleshoot common user sign in problems and errors in Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612735"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Risolvere i problemi di accesso dell'account con un dominio gestito di Servizi di dominio Azure ADTroubleshoot account sign-in problems with an Azure AD Domain Services managed domain

I motivi più comuni per un account utente che non può accedere a un dominio gestito di Azure AD DS includono gli scenari seguenti:The most common reasons for a user account that can't sign in to an Azure AD DS managed domain include the following scenarios:

* [L'account non è ancora sincronizzato in Servizi di dominio Active Directory di Azure.The account isn't synchronized into Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS non dispone degli iseutente con password per consentire l'accesso dell'account.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [L'account è bloccato.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS non può eseguire la sincronizzazione nelle credenziali per gli account esterni al tenant di Azure AD. Gli utenti esterni non possono accedere al dominio gestito di Servizi di dominio Active Directory di Azure.External users can't sign in to the Azure AD DS managed domain.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>L'account non è ancora sincronizzato in Azure AD DS

Depending on the size of your directory, it may take a while for user accounts and credential hashes to be available in Azure AD DS. Per le directory di grandi dimensioni, questa sincronizzazione unidirezionale iniziale da Azure AD può richiedere alcune ore e fino a un giorno o due. Assicurarsi di attendere abbastanza a lungo prima di ritentare l'autenticazione.

Per gli ambienti ibridi che l'utente Azure AD Connect per sincronizzare i dati della directory locale in Azure AD, assicurarsi di eseguire la versione più recente di Azure AD Connect e di aver [configurato Azure AD Connect per eseguire una sincronizzazione completa dopo l'abilitazione][azure-ad-connect-phs]di Servizi di dominio Active Directory di Azure . Se si disabilita Servizi di dominio Active Directory di Azure e quindi si riabilita, è necessario eseguire di nuovo questi passaggi.

Se si continuano ad avere problemi con gli account che non vengono sincronizzati tramite Azure AD Connect, riavviare il servizio di sincronizzazione di Azure AD. Dal computer in cui è installato Azure AD Connect aprire una finestra del prompt dei comandi ed eseguire i comandi seguenti:From the computer with Azure AD Connect installed, open a command prompt window and run the following commands:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS non dispone degli issi delle passwordAzure AD DS doesn't have the password hashes

Azure AD non genera né archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure AD DS per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Ambienti ibridi con sincronizzazione locale

Per gli ambienti ibridi che usano Azure AD Connect per la sincronizzazione da un ambiente DiS Active Directory locale, è possibile generare e sincronizzare localmente gli hashe s.r.l. Dopo aver creato il dominio gestito di Servizi di dominio Active Directory di Azure, abilitare la [sincronizzazione dell'hash delle password][azure-ad-connect-phs]con Servizi di dominio Azure Active Directory. Senza completare questo passaggio di sincronizzazione dell'hash delle password, non è possibile accedere a un account usando Servizi di dominio Active Directory di Azure.Without completing this password hash synchronization step, you can't sign in to an account using Azure AD DS. Se si disabilita Servizi di dominio Active Directory di Azure e quindi si riabilita, è necessario seguire di nuovo questi passaggi.

Per altre informazioni, vedere Funzionamento della [sincronizzazione dell'hash delle password per Azure AD DS.For][phs-process]more information, see How password hash synchronization works for Azure AD DS .

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Ambienti solo cloud senza sincronizzazione locale

Anche i domini gestiti di Servizi di dominio Active Directory di Azure senza sincronizzazione locale, solo gli account in Azure AD devono generare gli hash delle password NTLM o Kerberos necessari. Se un account solo cloud non riesce ad accedere, per l'account è stato completato correttamente un processo di modifica della password dopo l'abilitazione di Azure AD DS?

* **No, la password non è stata modificata.**
    * [Modificare la password dell'account][enable-user-accounts] per generare gli issi della password necessari, quindi attendere 15 minuti prima di provare ad accedere di nuovo.
    * Se si disabilita Servizi di dominio Active Directory di Azure e quindi riabilitare, ogni account deve seguire di nuovo i passaggi per modificare la password e generare gli isorni delle password necessari.
* **Sì, la password è stata modificata.**
    * Provare ad accedere utilizzando il formato `driley@aaddscontoso.com` *UPN,* ad esempio , `AADDSCONTOSO\deeriley`anziché il formato *SAMAccountName,* ad esempio .
    * Il *SAMAccountName* può essere generato automaticamente per gli utenti il cui prefisso UPN è troppo lungo o è lo stesso di un altro utente nel dominio gestito. È garantito che il formato *UPN* sia univoco all'interno di un tenant di Azure AD.

## <a name="the-account-is-locked-out"></a>L'account è bloccato

Un account utente in Servizi di dominio Active Directory di Azure viene bloccato quando viene raggiunta una soglia definita per i tentativi di accesso non riusciti. Questo comportamento di blocco dell'account è progettato per proteggerti da ripetuti tentativi di accesso con forza bruta che possono indicare un attacco digitale automatizzato.

Per impostazione predefinita, se sono presenti 5 tentativi con password errata in 2 minuti, l'account viene bloccato per 30 minuti.

Per altre informazioni e su come risolvere i problemi di blocco degli account, vedere Risolvere i problemi di blocco degli account in Servizi di dominio Active Directory di Azure.For more information and how to resolve account lockout issues, see [Troubleshoot account lockout issues in Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Passaggi successivi

Se si verificano ancora problemi durante l'aggiunta della macchina virtuale al dominio gestito di Azure AD DS, trovare la Guida e aprire un ticket di [supporto per Azure Active Directory.][azure-ad-support]

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
