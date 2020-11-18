---
title: Panoramica dei profili tecnici nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni sul modo in cui i profili tecnici vengono usati in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d61471e07dda8fcf0c715dcffe85ad3e39ed8ac3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840373"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informazioni sui profili tecnici nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profilo tecnico fornisce un Framework con un meccanismo incorporato per la comunicazione con tipi diversi di entità mediante un criterio personalizzato in Azure Active Directory B2C (Azure AD B2C). I profili tecnici vengono usati per comunicare con il tenant di Azure AD B2C, creare un utente o leggere un profilo utente. Un profilo tecnico può essere autocertificato per consentire l'interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso e quindi eseguire il rendering della pagina di iscrizione o di reimpostazione della password.

## <a name="type-of-technical-profiles"></a>Tipi di profili tecnici

Un profilo tecnico supporta i tipi di scenario riportati di seguito.

- [Application Insights](application-insights-technical-profile.md) l'invio di dati di evento a [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md): supporto della gestione degli utenti di Azure Active Directory B2C.
- [Azure AD multi-factor authentication](multi-factor-auth-technical-profile.md) : viene fornito il supporto per la verifica di un numero di telefono utilizzando Azure ad multi-factor authentication (multi-factor authentication). 
- [Trasformazione delle attestazioni](claims-transformation-technical-profile.md): chiamata di trasformazioni delle attestazioni di output per convalidare le attestazioni, modificarne i valori o impostare valori predefiniti per un set di attestazioni di output.
- [Hint token ID](id-token-hint.md) : convalida la `id_token_hint` firma del token JWT, il nome dell'autorità emittente e il destinatario del token ed estrae l'attestazione dal token in ingresso.
- [Autorità emittente di token JWT](jwt-issuer-technical-profile.md): rilascio di un token JWT che viene restituito all'applicazione relying party.
- [OAuth1](oauth1-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 2.0.
- [Password una sola volta](one-time-password-technical-profile.md) : fornisce il supporto per la gestione della generazione e della verifica di una password monouso.
- [OpenID Connect](openid-connect-technical-profile.md) -Federazione con qualsiasi provider di identità del protocollo OpenID Connect.
- [Phone Factor](phone-factor-technical-profile.md) : supporto per la registrazione e la verifica dei numeri di telefono.
- [Provider RESTful](restful-technical-profile.md) : è possibile chiamare i servizi API REST, ad esempio convalidare l'input dell'utente, arricchire i dati utente o integrarsi con applicazioni line-of-business.
- [Provider di identità SAML](saml-identity-provider-technical-profile.md) : Federazione con qualsiasi provider di identità del protocollo SAML.
- [Emittente del token SAML](saml-issuer-technical-profile.md) : genera un token SAML restituito all'applicazione relying party.
- [Autocertificazione](self-asserted-technical-profile.md): interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso, eseguire il rendering della pagina di iscrizione o reimpostare la password.
- [Gestione delle sessioni](custom-policy-reference-sso.md): gestione di diversi tipi di sessioni.

## <a name="technical-profile-flow"></a>Flusso dei profili tecnici

Tutti i tipi di profili tecnici condividono lo stesso concetto. Si inviano attestazioni di input, si esegue la trasformazione delle attestazioni e si comunica con l'entità configurata, ad esempio un provider di identità, un'API REST o servizi directory di Azure AD. Al termine del processo, il profilo tecnico restituisce le attestazioni di output ed è possibile che venga eseguita la trasformazione delle attestazioni di output. Il diagramma seguente illustra come vengono elaborati le trasformazioni e i mapping a cui viene fatto riferimento nel profilo tecnico. Indipendentemente dall'entità con cui interagisce il profilo tecnico, dopo l'esecuzione di qualsiasi trasformazione delle attestazioni, le attestazioni di output del profilo tecnico vengono immediatamente archiviate nel contenitore delle attestazioni.

![Diagramma che illustra il flusso del profilo tecnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)

1. **Gestione delle sessioni Single Sign-on (SSO)** : Ripristina lo stato della sessione del profilo tecnico, usando la [gestione delle sessioni SSO](custom-policy-reference-sso.md).
1. **Input Claims Transformation** : le attestazioni di input di ogni [trasformazione delle attestazioni](claimstransformations.md) di input vengono prelevate dall'elenco delle attestazioni.  Le attestazioni di output di una trasformazione delle attestazioni di input possono essere le attestazioni di input di una successiva trasformazione delle attestazioni di input.
1. **Attestazioni di input** : le attestazioni vengono prelevate dall'elenco di attestazioni e vengono usate per il profilo tecnico. Un [profilo tecnico autocertificato](self-asserted-technical-profile.md), ad esempio, usa le attestazioni di input per prepopolare le attestazioni di output fornite dall'utente. Un profilo tecnico API REST usa le attestazioni di input per inviare i parametri di input all'endpoint API REST. Azure Active Directory usa l'attestazione di input come identificatore univoco per la lettura, l'aggiornamento o l'eliminazione di un account.
1. **Esecuzione del profilo tecnico**: il profilo tecnico scambia le attestazioni con l'entità configurata. Ad esempio:
    - Reindirizza l'utente al provider di identità per completare l'accesso. Dopo aver completato l'accesso, l'utente torna indietro e l'esecuzione del profilo tecnico continua.
    - Chiama un'API REST inviando i parametri come InputClaims e ricevendo le informazioni restituite come OutputClaims.
    - Crea o aggiorna l'account utente.
    - Invia e verifica il messaggio di testo di MFA.
1. **Profili** tecnici di convalida: un [profilo tecnico autocertificato](self-asserted-technical-profile.md) può chiamare i [profili tecnici di convalida](validation-technical-profile.md). che convalida i dati inclusi nel profilo dall'utente e restituisce un messaggio di errore oppure OK, con o senza attestazioni di output. Prima che Azure AD B2C crei un nuovo account, ad esempio, controlla se l'utente esiste già nei servizi directory. È possibile chiamare un profilo tecnico API REST per aggiungere logica di business personalizzata.<p>L'ambito delle attestazioni di output di un profilo tecnico di convalida è limitato al profilo tecnico che richiama il profilo tecnico di convalida. e altri profili tecnici di convalida nello stesso profilo tecnico. Se si vogliono usare le attestazioni di output nel passaggio di orchestrazione successivo, è necessario aggiungerle al profilo tecnico che richiama il profilo tecnico di convalida.
1. **Attestazioni di output** : le attestazioni vengono restituite all'elenco di attestazioni. È possibile usare queste attestazioni nel passaggio di orchestrazione successivo oppure nelle trasformazioni delle attestazioni di output.
1. **Trasformazioni di output delle attestazioni** : le attestazioni di input di ogni [trasformazione delle attestazioni](claimstransformations.md) di output vengono prelevate dall'elenco delle attestazioni Le attestazioni di output del profilo tecnico provenienti dai passaggi precedenti possono essere le attestazioni di input di una trasformazione delle attestazioni di output. Al termine dell'esecuzione, le attestazioni di output vengono inserite nel contenitore delle attestazioni. Le attestazioni di output di una trasformazione delle attestazioni di output possono essere le attestazioni di input di una successiva trasformazione delle attestazioni di output.
1. **Gestione delle sessioni Single Sign-on (SSO)** : rende permanente i dati del profilo tecnico nella sessione, usando la [gestione delle sessioni SSO](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Inclusione profilo tecnico

Un profilo tecnico può includere un altro profilo tecnico per modificare le impostazioni o aggiungere nuove funzionalità.  L' `IncludeTechnicalProfile` elemento è un riferimento al profilo tecnico di base da cui deriva un profilo tecnico. Non sono previsti limiti per il numero di livelli.

Il profilo tecnico **AAD-UserReadUsingAlternativeSecurityId-NoError**, ad esempio, include **AAD-UserReadUsingAlternativeSecurityId**. Questo profilo tecnico imposta l' `RaiseErrorIfClaimsPrincipalDoesNotExist` elemento dei metadati su `true` e genera un errore se un account di social networking non esiste nella directory. **AAD-UserReadUsingAlternativeSecurityId-NOERROR** esegue l'override di questo comportamento e Disabilita il messaggio di errore.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** include il profilo tecnico `AAD-Common`.

```xml
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

**AAD-UserReadUsingAlternativeSecurityId-NOERROR** e **AAD-UserReadUsingAlternativeSecurityId** non specificano l'elemento del **protocollo** richiesto perché è specificato nel profilo tecnico **comune di AAD** .

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
