---
title: Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: Informazioni su come risolvere i problemi comuni di Collaborazione B2B di Azure Active Directory
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: f85c6bcc2abbd14c7879462f7013a97f550fdca5


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory

Questo articolo illustra come risolvere i problemi comuni di Collaborazione B2B di Azure Active Directory (Azure AD).

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>Non è possibile creare un utente esterno a causa di un contatto esistente

Se l'utente esterno che si vuole invitare ha già un oggetto contatto esistente, non è possibile invitare l'utente finché non viene risolto il conflitto, in genere rimuovendo l'oggetto contatto. Fino alla disponibilità generale di Collaborazione B2B, è necessario risolvere il conflitto manualmente.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>L'utente esterno aggiunto non viene visualizzato nella rubrica globale o nella selezione utenti

Nei casi in cui gli utenti esterni non vengono inseriti nell'elenco, potrebbero essere necessari alcuni minuti per la replica dell'oggetto.

## <a name="invitations-have-been-disabled-for-directory"></a>Gli inviti sono stati disabilitati per la directory

Se si riceve un messaggio di errore che indica che non si è autorizzati a invitare utenti, verificare che l'account utente abbia le autorizzazioni necessarie per invitare utenti esterni. A tale scopo è possibile usare le impostazioni utente:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Se di recente sono state modificate queste impostazioni o è stato assegnato il ruolo Mittente dell'invito guest a un utente, potrebbero essere necessari 15-60 minuti perché le modifiche abbiano effetto.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>L'utente invitato riceve un errore durante il riscatto

Di seguito sono riportati gli errori più comuni.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>L'amministratore dell'invitato non consente la creazione di utenti EmailVerified nel tenant

Questo si verifica quando si invitano utenti la cui organizzazione usa Azure Active Directory dove l'account dell'utente specifico non esiste, ad esempio se l'utente non esiste in contoso.com AAD. L'amministratore di contoso.com potrebbe aver impostato criteri che impediscono la creazione di utenti. L'utente esterno deve rivolgersi all'amministratore per determinare se gli utenti esterni sono consentiti. Per la creazione di utenti EmailVerified nel dominio, potrebbe essere necessario il consenso dell'amministratore. Per altre informazioni in proposito, vedere [questo articolo](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msolcompanysettings#parameters).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>L'utente esterno non esiste già in un dominio federato

Nei casi in cui l'utente esterno usa una soluzione di federazione in cui l'autenticazione viene eseguita in locale e l'utente non esiste già in Azure Active Directory, non è possibile invitare l'utente.

Per risolvere questo problema, l'amministratore dell'utente esterno deve sincronizzare l'account dell'utente con Azure Active Directory.

## <a name="how-does--which-is-normally-an-invalid-character-sync-with-azure-ad"></a>Sincronizzazione di "\#", che in genere è un carattere non valido, con Azure AD

Negli UPN "\#" è un carattere riservato per Collaborazione B2B di Azure AD o per gli utenti esterni, vale a dire che &lt;user@contoso.com&gt; invitato diventa &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt;. Non è quindi consentito l'accesso al portale di Azure a "\#" negli UPN locali.

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Viene visualizzato un errore durante l'aggiunta di utenti esterni a un gruppo sincronizzato

È possibile aggiungere utenti esterni solo a gruppi "assegnati" o "di sicurezza" e non a gruppi gestiti in locale.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>L'utente esterno non ha ricevuto un messaggio di posta elettronica da riscattare

L'invitato deve rivolgersi al provider di servizi Internet o controllare il filtro della posta indesiderata per verificare che sia consentito l'indirizzo seguente: &lt;Invites@microsoft.com&gt;

## <a name="my-recipient-received-multiple-emails-from-me"></a>Il destinatario ha ricevuto più messaggi di posta elettronica

Se il destinatario dell'invito ha più alias per l'account, potrebbe ricevere più di un invito. In questi casi il primo collegamento riscattato è l'account che viene creato quindi il secondo collegamento non è valido per il riscatto.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


