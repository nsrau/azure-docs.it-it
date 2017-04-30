---
title: Accesso condizionale per gli utenti di Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: "La Collaborazione B2B di Azure Active Directory supporta l&quot;autenticazione a più fattori (MFA) per l&quot;accesso selettivo alle applicazioni aziendali"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: fac4657e9b78e900c052384e212aa9f3915f970d
ms.lasthandoff: 04/13/2017


---

# <a name="conditional-access-for-b2b-collaboration-users"></a>Accesso condizionale per gli utenti di Collaborazione B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticazione a più fattori per gli utenti B2B
Con la Collaborazione B2B di Azure AD, le organizzazioni possono applicare in un modo esclusivo i criteri di autenticazione a più fattori per gli utenti B2B. Questi criteri possono essere applicati a livello di tenant, a livello app o a livello utente singolo, e possono essere anche abilitati per dipendenti a tempo pieno e membri dell'organizzazione. I criteri di autenticazione a più fattori (MFA) vengono applicati all'organizzazione delle risorse.

Ciò significa:
1. L'amministratore o l'information worker della società A invita gli utenti della società B a un'applicazione Foo della società A.
2. L'applicazione *Foo* nella società A è configurata per richiedere l'autenticazione a più fattori all'accesso.
3. Quando l'utente della società B cerca di accedere all'app Foo dal tenant della società A, gli viene chiesto di completare una richiesta di autenticazione a più fattori come previsto dai criteri di autenticazione a più fattori della società A.
4. L'utente può configurare la propria autenticazione a più fattori con la società A, scegliendo un'opzione di autenticazione a più fattori.
5. Questo funzionerà per qualsiasi identità, ad esempio Azure AD o l'account del servizio gestito se gli utenti della società B eseguono l'autenticazione usando l'ID basato su social network.
6. La società A dovrà avere SKU di Azure AD Premium adeguati che supportano l'autenticazione a più fattori. L'utente della società B utilizzerà questa licenza della società A.

In sintesi, la tenancy che invia l'invito è *sempre* responsabile dell'autenticazione a più fattori degli utenti di Collaborazione B2B dell'organizzazione partner e non dell'organizzazione partner stessa (anche se ha funzionalità di autenticazione a più fattori).

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configurazione dell'autenticazione a più fattori per gli utenti di Collaborazione B2B
La configurazione di MFA per gli utenti di Collaborazione B2B è estremamente semplice, come illustrato nel video seguente:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Esperienza di autenticazione a più fattori per gli utenti di B2B per il riscatto dell'offerta
Guardare l'animazione sotto per visualizzare l'esperienza di riscatto, come illustrato nel video seguente:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Reimpostazione dell'autenticazione a più fattori per gli utenti di Collaborazione B2B
L'amministratore attualmente può richiedere agli utenti di Collaborazione B2B di provare di nuovo l'identità solo usando i cmdlet di PowerShell seguenti. È quindi consigliabile usare i cmdlet di PowerShell seguenti se si vuole reimpostare il metodo di identificazione di un utente di Collaborazione B2B.

1. Connettersi ad Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Ottenere tutti gli utenti con metodi di identificazione

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Di seguito è fornito un esempio:

  ```
  PS C:\Users\tjwasserGet-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Reimpostare il metodo di autenticazione a più fattori per un utente specifico. È quindi possibile usare tale UserPrincipalName per eseguire il comando di reimpostazione per poter richiedere all'utente di Collaborazione B2B di impostare di nuovo i metodi di identificazione. Esempio:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Perché si esegue un'autenticazione a più fattori nella tenancy delle risorse?

Nella versione corrente, l'autenticazione a più fattori è sempre nella tenancy delle risorse. Il motivo è ovvio.

Ad esempio, si supponga che un utente di Contoso (Sara) riceve un invito da Fabrikam e Fabrikam ha abilitato l'autenticazione a più fattori per gli utenti B2B.

Se Contoso ha abilitato i criteri di autenticazione a più fattori per App1, ma non App2, e si esamina la richiesta di autenticazione a più fattori di Contoso nel token per determinare se Sara deve usare un'autenticazione a più fattori in Fabrikam o meno, è possibile che si verifichi il problema seguente:

* Giorno 1: Sara ha effettuato l'autenticazione a più fattori in Contoso perché accede all'App1, quindi non verrà visualizzata la richiesta di autenticazione a più fattori in Fabrikam.

* Giorno 2: Sara ha eseguito l'accesso all'App2 in Contoso, quindi quando accede a Fabrikam, deve eseguire la registrazione per l'autenticazione a più fattori in Fabrikam.

Ciò può risultare poco chiaro per Sara e molto probabilmente non consentirà di completare l'accesso.

In aggiunta, anche se Contoso ha una funzionalità di autenticazione a più fattori, non è sempre probabile che Fabrikam accetti i criteri di autenticazione a più fattori di Contoso.

Infine, l'autenticazione a più fattori del tenant delle risorse funziona anche per gli account del servizio gestito (MSA) e per gli ID social network per le organizzazioni partner che non hanno un'autenticazione a più fattori impostata.

Pertanto, il consiglio per gli utenti B2B è richiedere sempre l'autenticazione a più fattori. Questo potrebbe causare la duplicazione dell'autenticazione a più fattori in alcuni casi, ma ogni volta che si accede alla tenancy delle risorse, l'esperienza degli utenti finali è prevedibile: Sara si deve registrare per l'autenticazione a più fattori con la tenancy delle risorse.

### <a name="device-location-and-risk-based-conditional-access-for-b2b-users"></a>Dispositivo, posizione e accesso condizionale basato sui rischi per gli utenti B2B

Quando l'organizzazione Contoso abilita i criteri di accesso condizionale basato sul dispositivo, l'accesso non è permesso da dispositivi non gestiti, ovvero i dispositivi che non sono gestiti dall'organizzazione Contoso e non conformi ai criteri dei dispositivi di Contoso.

Se il dispositivo dell'utente B2B non è gestito da Contoso, vuol dire che l'accesso degli utenti B2B delle organizzazioni partner verrà bloccato in qualsiasi contesto siano stati applicati tali criteri.

È eccessivo pretendere che gli utenti di un'altra organizzazione abbiano i loro dispositivi gestiti dall'organizzazione che li invita. Pertanto, in aggiornamenti futuri, sarà possibile abilitare Contoso a considerare attendibile lo stato di conformità dei dispositivi di specifici partner. Ciò permetterebbe a Contoso di applicare i criteri per cui un utente di Fabrikam può accedere alle risorse di Contoso anche se sta usando un dispositivo gestito da Fabrikam.

Nel frattempo, Contoso può creare elenchi di esclusione che contengono utenti di partner specifici dai criteri di accesso condizionale basato sui dispositivi.

#### <a name="location-based-conditional-access-for-b2b"></a>Accesso condizionale basato sulla posizione per B2B

I criteri di accesso condizionale basato sulla posizione possono essere applicati per gli utenti B2B se l'organizzazione che invita (ad esempio, Contoso) è in grado di creare un perimetro di rete attendibile (ovvero, un intervallo di indirizzi IP) che definisce le organizzazioni partner (ad esempio, Fabrikam).

#### <a name="risk-based-conditional-access-for-b2b"></a>Accesso condizionale basato sui rischi per B2B

Non è possibile attualmente applicare criteri di accesso basato sui rischi per gli utenti B2B, perché la valutazione dei rischi viene eseguita nell'organizzazione dell'utente B2B (in altre parole, la tenancy di identità dell'utente B2B).

Negli aggiornamenti futuri, si sta considerando di creare una federazione del punteggio di rischio dei partner (con il consenso dei partner) in modo che Contoso possa proteggere le app e i dati condivisi esternamente non solo da rischi conosciuti, ma anche da rischi di cui Contoso non è a conoscenza perché potrebbero verificarsi in altri posti.

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

