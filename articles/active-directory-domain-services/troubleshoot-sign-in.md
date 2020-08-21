---
title: Risolvere i problemi di accesso in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come risolvere i problemi di accesso utente comuni e gli errori in Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 91c42e391d1a23e9ac494af9271c92e3e9b5d6c4
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722654"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Risolvere i problemi di accesso dell'account con un dominio gestito Azure Active Directory Domain Services

I motivi più comuni per un account utente che non riesce ad accedere a un dominio gestito di Azure Active Directory Domain Services (Azure AD DS) includono gli scenari seguenti:

* [L'account non è ancora sincronizzato in Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS non dispone degli hash delle password per consentire l'accesso dell'account.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [L'account è bloccato.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS non può sincronizzare le credenziali per gli account esterni al tenant di Azure AD. Gli utenti esterni non possono accedere al dominio gestito di Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>L'account non è ancora sincronizzato in Azure AD DS

A seconda delle dimensioni della directory, è possibile che gli account utente e gli hash delle credenziali siano disponibili in un dominio gestito. Per le directory di grandi dimensioni, la sincronizzazione iniziale unidirezionale da Azure AD può richiedere alcune ore e un massimo di un giorno o due. Assicurarsi di attendere abbastanza tempo prima di ritentare l'autenticazione.

Per gli ambienti ibridi che Azure AD Connect utente per sincronizzare i dati della directory locale in Azure AD, assicurarsi di eseguire la versione più recente di Azure AD Connect e di aver [configurato Azure ad Connect per eseguire una sincronizzazione completa dopo aver abilitato Azure AD DS][azure-ad-connect-phs]. Se si disattiva Azure AD DS e quindi si Abilita nuovamente, è necessario eseguire di nuovo questi passaggi.

Se continuano a verificarsi problemi con gli account che non vengono sincronizzati tramite Azure AD Connect, riavviare il servizio Azure AD Sync. Dal computer con Azure AD Connect installato, aprire una finestra del prompt dei comandi, quindi eseguire i comandi seguenti:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS non include gli hash delle password

Azure AD non genera né archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure AD DS per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Ambienti ibridi con sincronizzazione locale

Per gli ambienti ibridi che usano Azure AD Connect per eseguire la sincronizzazione da un ambiente Active Directory Domain Services locale, è possibile generare e sincronizzare localmente gli hash delle password NTLM o Kerberos necessari in Azure AD. Dopo aver creato il dominio gestito, [abilitare la sincronizzazione dell'hash delle password per Azure Active Directory Domain Services][azure-ad-connect-phs]. Senza completare questo passaggio di sincronizzazione dell'hash delle password, non è possibile accedere a un account tramite il dominio gestito. Se si disattiva Azure AD DS e quindi si Abilita nuovamente, è necessario eseguire di nuovo questi passaggi.

Per ulteriori informazioni, vedere funzionamento [della sincronizzazione dell'hash delle password per Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Ambienti solo cloud senza sincronizzazione locale

I domini gestiti senza sincronizzazione locale, solo gli account in Azure AD, devono anche generare gli hash delle password NTLM o Kerberos richiesti. Se non è possibile accedere a un account solo cloud, il processo di modifica della password è stato completato correttamente per l'account dopo aver abilitato Azure AD DS?

* **No, la password non è stata modificata.**
    * [Modificare la password dell'account][enable-user-accounts] per generare gli hash delle password richiesti, quindi attendere 15 minuti prima di riprovare ad accedere.
    * Se si disattiva Azure AD DS e quindi si riabilita, ogni account deve eseguire di nuovo i passaggi per modificare la password e generare gli hash delle password richiesti.
* **Sì, la password è stata modificata.**
    * Provare ad accedere usando il formato *UPN* , ad esempio `driley@aaddscontoso.com` , anziché il formato *sAMAccountName* come `AADDSCONTOSO\deeriley` .
    * Il *sAMAccountName* può essere generato automaticamente per gli utenti il cui prefisso UPN è troppo lungo o è uguale a un altro utente nel dominio gestito. Il formato *UPN* è sicuramente univoco all'interno di un tenant di Azure ad.

## <a name="the-account-is-locked-out"></a>L'account è bloccato

Un account utente in un dominio gestito viene bloccato quando viene soddisfatta una soglia definita per i tentativi di accesso non riusciti. Questo comportamento di blocco degli account è progettato per proteggere i tentativi di accesso con forza bruta ripetuta che potrebbero indicare un attacco digitale automatizzato.

Per impostazione predefinita, se sono presenti 5 tentativi di accesso con password errata in 2 minuti, l'account viene bloccato per 30 minuti.

Per altre informazioni e per risolvere i problemi di blocco degli account, vedere [risolvere i problemi di blocco degli account in Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Passaggi successivi

Se si verificano ancora problemi di aggiunta della macchina virtuale al dominio gestito, [vedere la guida e aprire un ticket di supporto per Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md