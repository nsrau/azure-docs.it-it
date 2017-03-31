---
title: "Autenticazione a più fattori per utenti di Collaborazione B2B di Azure Active Directory | Documentazione Microsoft"
description: "Collaborazione B2B di Azure Active Directory supporta l&quot;autenticazione a più fattori per l&quot;accesso selettivo alle applicazioni aziendali"
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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 32d0b45080d57712209e0c5a3e5adf981fb4b66e
ms.lasthandoff: 02/17/2017


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Autenticazione a più fattori per utenti di Collaborazione B2B di Azure Active Directory

In questo aggiornamento dell'anteprima pubblica di Collaborazione B2B di Azure AD le organizzazioni possono anche applicare criteri di autenticazione a più fattori per gli utenti di Collaborazione B2B. In questo aggiornamento l'autenticazione a più fattori viene sempre applicata nella tenancy delle risorse.

Ciò significa:
1. L'amministratore o l'information worker della società A invita gli utenti della società B a un'applicazione Foo della società A.
2. L'applicazione *Foo* nella società A è configurata per richiedere l'autenticazione a più fattori all'accesso.
3. Quando l'utente della società B cerca di accedere all'app Foo dal tenant della società A, gli viene chiesto di completare una richiesta di autenticazione a più fattori come previsto dai criteri di autenticazione a più fattori della società A.
4. L'utente può configurare la propria autenticazione a più fattori con la società A, scegliendo un'opzione di autenticazione a più fattori.
5. Questo funzionerà per qualsiasi identità, ad esempio Azure AD o l'account del servizio gestito se gli utenti della società B eseguono l'autenticazione usando l'ID basato su social network.
6. La società A dovrà avere SKU di Azure AD Premium adeguati che supportano l'autenticazione a più fattori. L'utente della società B utilizzerà questa licenza della società A.
7. In sintesi, la tenancy che invia l'invito è *sempre* responsabile dell'autenticazione a più fattori degli utenti di Collaborazione B2B dell'organizzazione partner e non dell'organizzazione partner stessa (anche se ha funzionalità di autenticazione a più fattori). Nelle versioni future verrà consentito alle organizzazioni che inviano l'invito di considerare attendibile l'autenticazione a più fattori di specifiche organizzazioni partner invece di usare l'autenticazione a più fattori dell'organizzazione che invia l'invito.

## <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configurazione dell'autenticazione a più fattori per gli utenti di Collaborazione B2B
La configurazione di MFA per gli utenti di Collaborazione B2B è estremamente semplice, come illustrato nel video seguente:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

## <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Esperienza di autenticazione a più fattori per gli utenti di B2B per il riscatto dell'offerta
Guardare l'animazione sotto per visualizzare l'esperienza di riscatto, come illustrato nel video seguente:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

## <a name="mfa-reset-for-b2b-collaboration-users"></a>Reimpostazione dell'autenticazione a più fattori per gli utenti di Collaborazione B2B
L'amministratore attualmente può richiedere agli utenti di Collaborazione B2B di provare di nuovo l'identità solo usando i cmdlet di PowerShell seguenti. È quindi consigliabile usare i cmdlet di PowerShell seguenti se si vuole reimpostare il metodo di identificazione di un utente di Collaborazione B2B.

> [!NOTE]
> Per usare il nuovo cmdlet, è necessario installare il modulo Azure AD PowerShell V2, che è possibile ottenere all'indirizzo https://www.powershellgallery.com/packages/AzureADPreview

1. Connessione ad Azure AD

  ```
  Connect-MsolService and login
  ```
2. Ottenere tutti gli utenti con metodi di identificazione

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Di seguito è fornito un esempio:

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Reimpostare il metodo di autenticazione a più fattori per un utente specifico. È quindi possibile usare tale UserPrincipalName per eseguire il comando di reimpostazione per poter richiedere all'utente di Collaborazione B2B di impostare di nuovo i metodi di identificazione. Esempio:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

