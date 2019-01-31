---
title: Accesso condizionale per gli utenti di Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: La Collaborazione B2B di Azure Active Directory supporta l'autenticazione a più fattori (MFA) per l'accesso selettivo alle applicazioni aziendali
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: d692392ba28899924ca42f7df47311d949633862
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080014"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Accesso condizionale per gli utenti di Collaborazione B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticazione a più fattori per gli utenti B2B
Con Collaborazione B2B di Azure AD, le organizzazioni possono applicare criteri di autenticazione a più fattori (MFA) per gli utenti B2B. Questi criteri possono essere applicati a livello di tenant, di app o di singolo utente, così come vengono abilitati per dipendenti a tempo pieno e membri dell'organizzazione. I criteri di autenticazione a più fattori (MFA) vengono applicati all'organizzazione delle risorse.

Esempio:
1. Un amministratore o un information worker della società A invita gli utenti della società B in un'applicazione *Foo* della società A.
2. L'applicazione *Foo* nella società A è configurata per richiedere l'autenticazione a più fattori all'accesso.
3. Quando l'utente della società B tenta di accedere all'app *Foo* nel tenant della società A, gli viene chiesto di completare una richiesta di autenticazione a più fattori.
4. L'utente può configurare la propria autenticazione a più fattori con la società A e ne sceglie l'opzione MFA.
5. Questo scenario funziona per qualsiasi identità, ad esempio di Azure AD o account del servizio gestito, se gli utenti della società B eseguono l'autenticazione usando un ID per social network.
6. La società A deve avere un numero sufficiente di licenze di Azure AD Premium che supportano l'autenticazione a più fattori. L'utente della società B utilizza la licenza della società A.

La tenancy che emette l'invito è sempre responsabile dell'autenticazione a più fattori degli utenti dell'organizzazione partner, anche se l'organizzazione partner ha funzionalità di autenticazione a più fattori.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configurazione dell'autenticazione a più fattori per gli utenti di Collaborazione B2B
La configurazione di MFA per gli utenti di Collaborazione B2B è estremamente semplice, come illustrato nel video seguente:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Esperienza di autenticazione a più fattori per gli utenti di B2B per il riscatto dell'offerta
Guardare l'animazione seguente per visualizzare l'esperienza di riscatto:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Reimpostazione dell'autenticazione a più fattori per gli utenti di Collaborazione B2B
L'amministratore attualmente può richiedere agli utenti di Collaborazione B2B di ripetere l'identificazione solo usando i cmdlet di PowerShell seguenti:

1. Connessione ad Azure AD

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
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Reimpostare il metodo di autenticazione a più fattori per un utente specifico in modo da richiedere all'utente di Collaborazione B2B di impostare di nuovo i metodi di identificazione. Esempio:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Perché si esegue un'autenticazione a più fattori nella tenancy delle risorse?

Nella versione corrente, l'autenticazione a più fattori è sempre nella tenancy delle risorse, per motivi di prevedibilità. Ad esempio, si supponga che un utente di Contoso (Sara) riceve un invito da Fabrikam e Fabrikam ha abilitato l'autenticazione a più fattori per gli utenti B2B.

Se Contoso ha abilitato i criteri di autenticazione a più fattori per App1 ma non per App2, esaminando l'attestazione MFA di Contoso nel token si potrebbe riscontrare il problema seguente.

* Giorno 1: un utente con autenticazione a più fattori in Contoso accede ad App1 e non viene quindi visualizzata una richiesta MFA aggiuntiva in Fabrikam.

* Giorno 2: l'utente ha eseguito l'accesso ad App2 in Contoso e quando accede a Fabrikam deve eseguirvi la registrazione al servizio MFA.

Questo processo può generare confusione e determinare una riduzione del numero di accessi completati.

In aggiunta, anche se Contoso ha una funzionalità di autenticazione a più fattori, non è sempre probabile che Fabrikam accetti i criteri di autenticazione a più fattori di Contoso.

Infine, l'autenticazione a più fattori del tenant delle risorse funziona anche per account del servizio gestito, ID per social network e organizzazioni partner che non hanno configurato l'autenticazione a più fattori.

Di conseguenza, per l'autenticazione a più fattori degli utenti B2B è consigliabile richiedere sempre l'autenticazione a più fattori nel tenant che emette l'invito. In alcuni casi, questo requisito può causare la duplicazione dell'autenticazione a più fattori, ma l'esperienza degli utenti finali è prevedibile a ogni accesso al tenant che emette l'invito: l'utente deve eseguire la registrazione al servizio MFA con tale tenant.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Accesso condizionale basato sul dispositivo, sulla posizione e sui rischi per gli utenti B2B

Quando Contoso abilita criteri di accesso condizionale basati sul dispositivo per i dati aziendali, viene impedito l'accesso da dispositivi non gestiti da Contoso e non conformi ai criteri dei dispositivi di Contoso.

Se il dispositivo dell'utente B2B non è gestito da Contoso, l'accesso degli utenti B2B delle organizzazioni partner viene bloccato in qualsiasi contesto vengano applicati i criteri. Contoso, tuttavia, può creare elenchi di esclusione contenenti gli utenti di partner specifici per escluderli dai criteri di accesso condizionale basati sul dispositivo.

#### <a name="location-based-conditional-access-for-b2b"></a>Accesso condizionale basato sulla posizione per B2B

I criteri di accesso condizionale basati sulla posizione possono essere applicati per gli utenti B2B se l'organizzazione che emette l'invito può creare un intervallo di indirizzi IP attendibili che definisce le organizzazioni partner.

#### <a name="risk-based-conditional-access-for-b2b"></a>Accesso condizionale basato sui rischi per B2B

Attualmente non è possibile applicare criteri di accesso basati sui rischi agli utenti B2B, perché la valutazione dei rischi viene eseguita nell'organizzazione principale dell'utente B2B.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Licenze per la Collaborazione B2B di Azure AD](licensing-guidance.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](faq.md)
