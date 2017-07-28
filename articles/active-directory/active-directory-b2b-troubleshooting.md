---
title: Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory | Microsoft Docs
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
ms.date: 05/25/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 42229b338063634480551f26896963d8add5e071
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory

Questo articolo illustra come risolvere i problemi comuni di Collaborazione B2B di Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>L'utente esterno aggiunto non viene visualizzato nella rubrica globale o nella selezione utenti

Nei casi in cui gli utenti esterni non vengono inseriti nell'elenco, potrebbero essere necessari alcuni minuti per la replica dell'oggetto.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Un utente guest B2B non compare nella selezione utenti di SharePoint Online/OneDrive 
 
La possibilità di cercare gli utenti guest esistenti nella selezione utenti di SharePoint Online è disattivata per impostazione predefinita per corrispondenza con il comportamento legacy.

È possibile abilitare questa funzionalità usando l'impostazione "ShowPeoplePickerSuggestionsForGuestUsers" a livello di tenant e di raccolta siti. Può essere impostata con i cmdlet Set-SPOTenant e Set-SPOSite, che consentono ai membri di cercare tutti gli utenti guest esistenti nella directory. Le modifiche nell'ambito tenant non influiscono sui siti di SPO di cui si è già stato eseguito il provisioning.

## <a name="invitations-have-been-disabled-for-directory"></a>Gli inviti sono stati disabilitati per la directory

Se si riceve una notifica che indica che non si è autorizzati a invitare utenti, verificare che l'account utente abbia le autorizzazioni necessarie per invitare utenti esterni in Impostazioni utente:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Se di recente sono state modificate queste impostazioni o è stato assegnato il ruolo Mittente dell'invito guest a un utente, potrebbero essere necessari 15-60 minuti perché le modifiche abbiano effetto.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>L'utente invitato riceve un errore durante il riscatto

Di seguito sono riportati gli errori più comuni.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>L'amministratore dell'invitato non consente la creazione di utenti EmailVerified nel tenant

Questo si verifica quando si invitano utenti la cui organizzazione usa Azure Active Directory, ma dove l'account dell'utente specifico non esiste (ad esempio se l'utente non esiste in contoso.com di Azure AD). L'amministratore di contoso.com potrebbe aver impostato criteri che impediscono la creazione di utenti. L'utente deve rivolgersi all'amministratore per determinare se gli utenti esterni sono consentiti. L'amministratore dell'utente esterno potrebbe dover consentire gli utenti verificati tramite posta elettronica nel dominio (vedere questo [articolo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) su come consentire gli utenti verificati tramite posta elettronica).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>L'utente esterno non esiste già in un dominio federato

Se si usa l'autenticazione con federazione e l'utente non esiste già in Azure Active Directory, non è possibile invitare l'utente.

Per risolvere questo problema, l'amministratore dell'utente esterno deve sincronizzare l'account dell'utente con Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>In che modo "\#", che in genere è un carattere non valido, viene sincronizzato con Azure AD?

Negli UPN, "\#" è un carattere riservato per Collaborazione B2B di Azure AD o per gli utenti esterni, perché l'account invitato user@contoso.com diventa user_contoso.com#EXT@fabrikam.onmicrosoft.com. Non è quindi consentito l'accesso al portale di Azure a \# negli UPN locali. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Viene visualizzato un errore durante l'aggiunta di utenti esterni a un gruppo sincronizzato

È possibile aggiungere utenti esterni solo a gruppi "assegnati" o "di sicurezza" e non a gruppi gestiti in locale.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>L'utente esterno non ha ricevuto un messaggio di posta elettronica da riscattare

L'invitato deve rivolgersi al provider di servizi Internet o controllare il filtro della posta indesiderata per verificare che sia consentito l'indirizzo seguente: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Il messaggio personalizzato a volte non viene incluso con i messaggi di invito

Per garantire la conformità alle leggi sulla privacy, le API non includono messaggi personalizzati nell'invito tramite posta elettronica nei casi seguenti:

- Il mittente dell'invito non ha un indirizzo di posta elettronica nel tenant che emette l'invito
- Un'entità servizio app invia l'invito

Se questo scenario è importante per l'utente, è possibile eliminare il messaggio di posta elettronica di invito dell'API e inviarlo tramite il meccanismo di posta elettronica preferito. Richiedere al consulente legale della propria organizzazione di verificare che qualsiasi messaggio di posta elettronica inviato in questo modo sia conforme alle leggi sulla privacy.

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

