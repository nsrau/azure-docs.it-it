---
title: Configurare il comportamento della sessione con criteri personalizzati-Azure Active Directory B2C | Microsoft Docs
description: Configurare il comportamento della sessione utilizzando criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31257d795dbd06da65e3d07e18a16d9bdf7e782a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961103"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare il comportamento della sessione utilizzando criteri personalizzati in Azure Active Directory B2C

La gestione delle [sessioni Single Sign-on (SSO)](session-overview.md) in Azure Active Directory B2C (Azure ad B2C) consente a un amministratore di controllare l'interazione con un utente dopo che l'utente è già stato autenticato. Ad esempio, l'amministratore può controllare se la selezione dei provider di identità viene visualizzata o se è necessario immettere di nuovo i dettagli dell'account. In questo articolo viene descritto come configurare le impostazioni di SSO per Azure AD B2C.

## <a name="session-behavior-properties"></a>Proprietà del comportamento della sessione

È possibile usare le proprietà seguenti per gestire le sessioni delle applicazioni Web:

- **Durata della sessione dell'app Web (minuti)**: durata del cookie della sessione di Azure AD B2C archiviato nel browser dell'utente dopo un'autenticazione corretta.
  - Impostazione predefinita = 86400 secondi (1440 minuti).
  - Minimo (inclusivo) = 900 secondi (15 minuti).
  - Massimo (inclusivo) = 86400 secondi (1440 minuti).
- **Timeout della sessione dell'app Web** : [tipo di scadenza della sessione](session-overview.md#session-expiry-type), in *sequenza*o *assoluto*. 
- **Configurazione di Single Sign-on** : [ambito della sessione](session-overview.md#session-scope) del comportamento di Single Sign-on (SSO) tra più app e flussi utente nel tenant di Azure ad B2C. 

## <a name="configure-the-properties"></a>Configurare le proprietà

Per modificare le configurazioni del comportamento della sessione e dell'accesso SSO, è necessario aggiungere un elemento **UserJourneyBehaviors** all'interno dell'elemento [RelyingParty](relyingparty.md).  L'elemento **UserJourneyBehaviors** deve essere immediatamente successivo a **DefaultUserJourney**. L'elemento **UserJourneyBehavors** dovrebbe essere simile all'esempio seguente:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="configure-sign-out-behavior"></a>Configurare il comportamento di disconnessione

### <a name="secure-your-logout-redirect"></a>Proteggere il reindirizzamento di disconnessione

Dopo la disconnessione, l'utente viene reindirizzato all'URI specificato nel `post_logout_redirect_uri` parametro, indipendentemente dagli URL di risposta specificati per l'applicazione. Tuttavia, se viene passato un oggetto valido `id_token_hint` e il **token ID richiesto nelle richieste di disconnessione** è attivato, Azure ad B2C verifica che il valore `post_logout_redirect_uri` corrisponda a uno degli URI di reindirizzamento configurati dell'applicazione prima di eseguire il reindirizzamento. Se per l'applicazione non è stato configurato alcun URL di risposta corrispondente, viene visualizzato un messaggio di errore e l'utente non viene reindirizzato. 

Per richiedere un token ID nelle richieste di disconnessione, aggiungere un elemento **UserJourneyBehaviors** all'interno dell'elemento [RelyingParty](relyingparty.md) . Impostare quindi il **EnforceIdTokenHintOnLogout** dell'elemento **SingleSignOn** su `true` . L'elemento **UserJourneyBehaviors** dovrebbe essere simile all'esempio seguente:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

Per configurare l'URL di disconnessione dell'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **registrazioni app**, quindi selezionare l'applicazione.
1. Selezionare **Autenticazione**.
1. Nella casella di testo **URL di disconnessione** Digitare l'URI di reindirizzamento post-disconnessione e quindi selezionare **Salva**.

### <a name="single-sign-out"></a>Single Sign-Out

#### <a name="configure-the-applications"></a>Configurare le applicazioni

Quando si reindirizza l'utente all'endpoint di disconnessione Azure AD B2C (per i protocolli OAuth2 e SAML), Azure AD B2C Cancella la sessione dell'utente dal browser.  Per consentire [Single Sign-out](session-overview.md#single-sign-out), impostare l'oggetto `LogoutUrl` dell'applicazione dal portale di Azure:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Scegliere la directory Azure AD B2C facendo clic sull'account nell'angolo superiore destro della pagina.
1. Nel menu a sinistra scegliere **Azure ad B2C**, selezionare **registrazioni app**, quindi selezionare l'applicazione.
1. Selezionare **Autenticazione**.
1. Nella casella di testo **URL di disconnessione** Digitare l'URI di reindirizzamento post-disconnessione e quindi selezionare **Salva**.

#### <a name="configure-the-token-issuer"></a>Configurare l'emittente del token 

Per supportare single sign-out, i profili tecnici dell'emittente di token per JWT e SAML devono specificare:

- Nome del protocollo, ad esempio `<Protocol Name="OpenIdConnect" />`
- Riferimento al profilo tecnico della sessione, ad esempio `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Nell'esempio seguente vengono illustrate le autorità emittenti dei token JWT e SAML con Single Sign-out:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [sessione Azure AD B2C](session-overview.md).
