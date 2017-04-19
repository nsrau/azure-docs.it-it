---
title: 'Azure Active Directory Domain Services: abilitare la sincronizzazione password | Microsoft Docs'
description: Introduzione ad Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 51e4665b6372859021237144a0bf7f6763a2638b
ms.lasthandoff: 04/14/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Abilitare la sincronizzazione password con Azure Active Directory Domain Services
Nelle attività precedenti è stato abilitato Azure Active Directory Domain Services (AD DS) per il tenant di Azure Active Directory (Azure AD). L'attività successiva consiste nell'abilitare gli hash delle credenziali, necessari per l'autenticazione NTLM (NT LAN Manager) e Kerberos per la sincronizzazione con Azure Active Directory Domain Services. Al termine della configurazione della sincronizzazione delle credenziali, gli utenti potranno accedere al dominio gestito con le credenziali aziendali.

Le procedure variano a seconda che l'organizzazione abbia un tenant di Azure AD solo cloud o sia impostata per la sincronizzazione con la directory locale tramite Azure AD Connect.

> [!div class="op_single_selector"]
> * [Tenant di Azure AD solo cloud](active-directory-ds-getting-started-password-sync.md)
> * [Tenant di Azure AD sincronizzato](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Attività 5: Abilitare la sincronizzazione password con Azure Active Directory Domain Services per un tenant di Azure AD solo cloud
Per autenticare gli utenti nel dominio gestito, Azure Active Directory Domain Services necessita di hash delle credenziali in un formato idoneo per l'autenticazione NTLM e Kerberos. Se non si abilita Azure Active Directory Domain Services per il tenant, Azure AD non genera né archivia gli hash delle credenziali nel formato necessario per l'autenticazione NTLM o Kerberos. Per ovvi motivi di sicurezza, Azure AD non archivia nemmeno le credenziali in un formato non crittografato. Azure AD quindi non può generare questi hash delle credenziali NTLM o Kerberos in base alle credenziali esistenti degli utenti.

> [!NOTE]
> Se l'organizzazione ha un tenant di Azure AD solo cloud, gli utenti che devono usare Azure Active Directory Domain Services devono cambiare le proprie password.
>
>

Questo processo di modifica delle password determina la generazione in Azure AD degli hash delle credenziali richiesti da Azure Active Directory Domain Services per l'autenticazione Kerberos e NTLM. È possibile impostare come scadute le password per tutti gli utenti del tenant che devono usare Azure Active Directory Domain Services oppure richiedere a tali utenti di cambiare le proprie password.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Abilitare la generazione di hash di credenziali NTLM e Kerberos per un tenant di Azure AD solo cloud
Di seguito sono riportate le istruzioni che è necessario fornire agli utenti finali affinché possano cambiare le proprie password:

1. Andare alla pagina del [pannello di accesso di Azure AD](http://myapps.microsoft.com) per l'organizzazione.
2. Nella finestra del pannello di accesso selezionare la scheda **profilo**.
3. Fare clic sul riquadro **Cambia password**.

    ![Riquadro "Cambia password" nel pannello di accesso di Azure AD](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Se l'opzione **Cambia password** non è visualizzata nella finestra del pannello di accesso, verificare che l'organizzazione abbia configurato la [gestione delle password in Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. Nella pagina **Cambia password** digitare la password esistente (precedente) e quindi digitare e confermare una nuova password.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Fare clic su **invia**.

Dopo alcuni minuti dalla modifica, la nuova password è utilizzabile in Azure Active Directory Domain Services. Dopo alcuni minuti aggiuntivi (in genere circa 20), è possibile accedere ai computer aggiunti al dominio gestito usando la password appena cambiata.

## <a name="next-steps"></a>Passaggi successivi
* [Come aggiornare la password](../active-directory/active-directory-passwords-update-your-own-password.md#reset-my-password)
* [Introduzione alla gestione delle password in Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Abilitare la sincronizzazione password in Azure Active Directory Domain Services per un tenant di Azure AD sincronizzato](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Amministrare un dominio gestito di Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Aggiungere una macchina virtuale Windows a un dominio gestito di Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

