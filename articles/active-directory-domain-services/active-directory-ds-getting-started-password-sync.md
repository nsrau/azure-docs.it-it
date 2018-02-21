---
title: 'Azure Active Directory Domain Services: abilitare la sincronizzazione password | Microsoft Docs'
description: Introduzione ad Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2017
ms.author: maheshu
ms.openlocfilehash: ea65659b1d35246bc3e7235b3faed8d2a5368010
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Abilitare la sincronizzazione password con Azure Active Directory Domain Services
Nelle attività precedenti è stato abilitato Azure Active Directory Domain Services per il tenant di Azure Active Directory (Azure AD). L'attività successiva prevede l'abilitazione della sincronizzazione degli hash delle credenziali necessari per l'autenticazione NTLM (NT LAN Manager) e Kerberos con Azure AD Domain Services. Al termine della configurazione della sincronizzazione delle credenziali, gli utenti potranno accedere al dominio gestito con le credenziali aziendali.

La procedura da eseguire è diversa per gli account utente solo cloud rispetto agli account utente sincronizzati dalla directory locale tramite Azure AD Connect. 

<br>
| **Tipo di account utente** | **Passaggi da eseguire** |
| --- |---|
| **Account utente cloud creati in Azure AD** |**&#x2713;** [Seguire le istruzioni in questo articolo](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Account utente sincronizzati da una directory locale** |**&#x2713;** [Sincronizzare le password per gli account utente sincronizzati dall'istanza locale di AD con il dominio gestito](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Potrebbe essere necessario completare entrambi i set di passaggi.**
> Se il tenant di Azure AD include una combinazione di utenti solo cloud e utenti dell'istanza locale di AD, è necessario eseguire entrambi i set di passaggi.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Attività 5: Abilitare la sincronizzazione password nel dominio gestito per gli account utente solo cloud
Per autenticare gli utenti nel dominio gestito, Azure Active Directory Domain Services necessita di hash delle credenziali in un formato idoneo per l'autenticazione NTLM e Kerberos. Azure AD genera e archivia gli hash delle credenziali nel formato necessario per l'autenticazione NTLM o Kerberos solo dopo l'abilitazione di Azure Active Directory Domain Services per il tenant. Per ovvi motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle credenziali NTLM o Kerberos in base alle credenziali esistenti degli utenti.

> [!NOTE]
> **Se l'organizzazione include account utente solo cloud, tutti gli utenti che devono usare Azure Active Directory Domain Services devono cambiare le proprie password.** Un account utente solo cloud è un account creato nella directory di Azure AD tramite il portale di Azure o i cmdlet di Azure AD PowerShell. Questi account utente non vengono sincronizzati da una directory locale.
>
>

Questo processo di modifica delle password determina la generazione in Azure AD degli hash delle credenziali richiesti da Azure Active Directory Domain Services per l'autenticazione Kerberos e NTLM. È possibile impostare come scadute le password per tutti gli utenti del tenant che devono usare Azure Active Directory Domain Services oppure richiedere a tali utenti di cambiare le proprie password.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-user-account"></a>Abilitare la generazione di hash di credenziali NTLM e Kerberos per account utente solo cloud
Di seguito sono riportate le istruzioni che è necessario fornire agli utenti finali affinché possano cambiare le proprie password:

1. Andare alla pagina del [pannello di accesso di Azure AD](http://myapps.microsoft.com) per l'organizzazione.

    ![Avviare il pannello di accesso di Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. Nell'angolo superiore destro fare clic sul nome e scegliere **Profilo** dal menu.

    ![Selezionare un profilo](./media/active-directory-domain-services-getting-started/select-profile.png)

3. Nella pagina **Profilo** fare clic su **Cambia password**.

    ![Fare clic su "Cambia password"](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Se l'opzione **Cambia password** non è visualizzata nella finestra del pannello di accesso, verificare che l'organizzazione abbia configurato la [gestione delle password in Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. Nella pagina **Cambia password** digitare la password esistente (precedente) e quindi digitare e confermare una nuova password.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Fare clic su **invia**.

Dopo alcuni minuti dalla modifica, la nuova password è utilizzabile in Azure Active Directory Domain Services. Dopo circa 20 minuti, è possibile accedere ai computer aggiunti al dominio gestito usando la password appena modificata.

## <a name="related-content"></a>Contenuti correlati
* [Come aggiornare la password](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Introduzione alla gestione delle password in Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Abilitare la sincronizzazione password in Azure Active Directory Domain Services per un tenant di Azure AD sincronizzato](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Amministrare un dominio gestito di Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Aggiungere una macchina virtuale Windows a un dominio gestito di Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
