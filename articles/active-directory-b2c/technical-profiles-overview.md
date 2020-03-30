---
title: Panoramica dei profili tecnici nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni sull'utilizzo dei profili tecnici in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057306"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informazioni sui profili tecnici nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profilo tecnico fornisce un framework con un meccanismo incorporato per comunicare con diversi tipi di parti usando criteri personalizzati in Azure Active Directory B2C (Azure AD B2C). I profili tecnici vengono usati per comunicare con il tenant di Azure AD B2C, creare un utente o leggere un profilo utente. Un profilo tecnico può essere autocertificato per consentire l'interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso e quindi eseguire il rendering della pagina di iscrizione o di reimpostazione della password.

## <a name="type-of-technical-profiles"></a>Tipi di profili tecnici

Un profilo tecnico supporta i tipi di scenario riportati di seguito.

- [Application Insights](application-insights-technical-profile.md) - Invio di dati di eventi ad [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md): supporto della gestione degli utenti di Azure Active Directory B2C.
- [Azure Multi-Factor Authentication:](multi-factor-auth-technical-profile.md) fornisce supporto per la verifica di un numero di telefono tramite Azure Multi-Factor Authentication (MFA). 
- [Trasformazione delle attestazioni](claims-transformation-technical-profile.md): chiamata di trasformazioni delle attestazioni di output per convalidare le attestazioni, modificarne i valori o impostare valori predefiniti per un set di attestazioni di output.
- [Autorità emittente di token JWT](jwt-issuer-technical-profile.md): rilascio di un token JWT che viene restituito all'applicazione relying party.
- [OAuth1](oauth1-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 2.0.
- [Password mono(abitrare)](one-time-password-technical-profile.md) - Fornisce supporto per la gestione della generazione e della verifica di una password monodata.
- [OpenID Connect](openid-connect-technical-profile.md) - Federazione con qualsiasi provider di identità del protocollo OpenID Connect.
- [Fattore di telefono:](phone-factor-technical-profile.md) supporto per la registrazione e la verifica dei numeri di telefono.
- [Provider RESTful:](restful-technical-profile.md) chiamata ai servizi dell'API REST, ad esempio convalida l'input dell'utente, arricchisce i dati utente o si integra con applicazioni line-of-business.
- [SAML2](saml-technical-profile.md): federazione con qualsiasi provider di identità con protocollo SAML.
- [Autorità emittente di token SAML:](saml-issuer-technical-profile.md) genera un token SAML restituito all'applicazione relying party.
- [Autocertificazione](self-asserted-technical-profile.md): interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso, eseguire il rendering della pagina di iscrizione o reimpostare la password.
- [Gestione delle sessioni](custom-policy-reference-sso.md): gestione di diversi tipi di sessioni.

## <a name="technical-profile-flow"></a>Flusso dei profili tecnici

Tutti i tipi di profili tecnici condividono lo stesso concetto. Si inviano attestazioni di input, si esegue la trasformazione delle attestazioni e si comunica con l'entità configurata, ad esempio un provider di identità, un'API REST o servizi directory di Azure AD. Al termine del processo, il profilo tecnico restituisce le attestazioni di output e può eseguire la trasformazione delle attestazioni di output. Il diagramma seguente illustra come vengono elaborati le trasformazioni e i mapping a cui viene fatto riferimento nel profilo tecnico. Indipendentemente dall'entità con cui interagisce il profilo tecnico, dopo l'esecuzione di qualsiasi trasformazione delle attestazioni, le attestazioni di output del profilo tecnico vengono immediatamente archiviate nel contenitore delle attestazioni.

![Diagramma che illustra il flusso del profilo tecnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Gestione sessione Single Sign-On (SSO):** ripristina lo stato della sessione del profilo tecnico, utilizzando [la gestione della sessione SSO](custom-policy-reference-sso.md).
1. **Trasformazione delle attestazioni** di input: le attestazioni di input di ogni [trasformazione delle attestazioni](claimstransformations.md) di input vengono prelevate dal contenitore delle attestazioni.  Le attestazioni di output di una trasformazione delle attestazioni di input possono essere le attestazioni di input di una successiva trasformazione delle attestazioni di input.
1. **Attestazioni** di input: i reclami vengono prelevati dal sacchetto dei sinistri e vengono utilizzati per il profilo tecnico. Un [profilo tecnico autocertificato](self-asserted-technical-profile.md), ad esempio, usa le attestazioni di input per prepopolare le attestazioni di output fornite dall'utente. Un profilo tecnico API REST usa le attestazioni di input per inviare i parametri di input all'endpoint API REST. Azure Active Directory usa l'attestazione di input come identificatore univoco per la lettura, l'aggiornamento o l'eliminazione di un account.
1. **Esecuzione del profilo tecnico**: il profilo tecnico scambia le attestazioni con l'entità configurata. Ad esempio:
    - Reindirizza l'utente al provider di identità per completare l'accesso. Dopo aver completato l'accesso, l'utente torna indietro e l'esecuzione del profilo tecnico continua.
    - Chiama un'API REST inviando i parametri come InputClaims e ricevendo le informazioni restituite come OutputClaims.
    - Crea o aggiorna l'account utente.
    - Invia e verifica il messaggio di testo di MFA.
1. **Profili tecnici** di convalida - Un [profilo tecnico auto-affermi](self-asserted-technical-profile.md) può chiamare profili tecnici di [convalida](validation-technical-profile.md). che convalida i dati inclusi nel profilo dall'utente e restituisce un messaggio di errore oppure OK, con o senza attestazioni di output. Prima che Azure AD B2C crei un nuovo account, ad esempio, controlla se l'utente esiste già nei servizi directory. È possibile chiamare un profilo tecnico API REST per aggiungere logica di business personalizzata.<p>L'ambito delle rivendicazioni di output di un profilo tecnico di convalida è limitato al profilo tecnico che richiama il profilo tecnico di convalida. e altri profili tecnici di validazione con lo stesso profilo tecnico. Se si vogliono usare le attestazioni di output nel passaggio di orchestrazione successivo, è necessario aggiungerle al profilo tecnico che richiama il profilo tecnico di convalida.
1. **Attestazioni** di output: i reclami vengono restituiti al sacchetto per le richieste di risarcimento. È possibile usare queste attestazioni nel passaggio di orchestrazione successivo oppure nelle trasformazioni delle attestazioni di output.
1. **Trasformazioni delle attestazioni** di output: le attestazioni di input di ogni [trasformazione delle attestazioni](claimstransformations.md) di output vengono prelevate dal contenitore delle attestazioni. Le attestazioni di output del profilo tecnico provenienti dai passaggi precedenti possono essere le attestazioni di input di una trasformazione delle attestazioni di output. Al termine dell'esecuzione, le attestazioni di output vengono inserite nel contenitore delle attestazioni. Le attestazioni di output di una trasformazione delle attestazioni di output possono essere le attestazioni di input di una successiva trasformazione delle attestazioni di output.
1. **Gestione delle sessioni Single Sign-On (SSO)** - Rende persistenti i dati del profilo tecnico nella sessione, utilizzando [gestione sessioni SSO](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Inclusione del profilo tecnico

Un profilo tecnico può includere un altro profilo tecnico per modificare le impostazioni o aggiungere nuove funzionalità.  L'elemento `IncludeTechnicalProfile` è un riferimento al profilo tecnico di base da cui deriva un profilo tecnico. Non sono previsti limiti per il numero di livelli.

Il profilo tecnico **AAD-UserReadUsingAlternativeSecurityId-NoError**, ad esempio, include **AAD-UserReadUsingAlternativeSecurityId**. Questo profilo tecnico `RaiseErrorIfClaimsPrincipalDoesNotExist` imposta `true`l'elemento di metadati su e genera un errore se non esiste un account social nella directory. **AAD-UserReadUsingAlternativeSecurityId-NoError** esegue l'override di questo comportamento e disabilita tale messaggio di errore.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** include il profilo tecnico `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Sia **AAD-UserReadUsingAlternativeSecurityId-NoError** **che AAD-UserReadUsingAlternativeSecurityId** non specificano l'elemento **Protocol** richiesto, perché è specificato nel profilo tecnico **AAD-Common.**

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
