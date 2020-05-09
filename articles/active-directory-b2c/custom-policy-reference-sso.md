---
title: Gestione delle sessioni Single Sign-on con criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni sulla gestione delle sessioni SSO tramite criteri personalizzati in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4aa9f4839c8bfc04cee4bb03ea0eac98cb8b25c0
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926120"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestione delle sessioni Single Sign-On in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Per la gestione delle [sessioni Single Sign-on (SSO)](session-overview.md) viene utilizzata la stessa semantica di qualsiasi altro profilo tecnico nei criteri personalizzati. Quando si esegue un passaggio di orchestrazione, nel profilo tecnico associato al passaggio viene eseguita la query di un riferimento a `UseTechnicalProfileForSessionManagement`. Se ne esiste uno, il provider della sessione SSO a cui si fa riferimento viene controllato per verificare se l'utente sia un partecipante alla sessione. In caso affermativo, il provider della sessione SSO viene usato per ripopolare la sessione. In modo analogo, al termine dell'esecuzione di un passaggio di orchestrazione, il provider viene usato per archiviare le informazioni nella sessione, se è stato specificato un provider di sessione SSO.

In Azure AD B2C sono stati definiti alcuni provider di sessione SSO che è possibile usare:

|Provider di sessioni  |Ambito  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Nessuno       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C gestore sessioni interno.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Tra Azure AD B2C e il provider di identità OAuth1, OAuth2 o OpenId Connect.        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Tra un OAuth2 o OpenId Connect relying party applicazione e Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Tra Azure AD B2C e il provider di identità SAML. E tra un provider di servizi SAML (relying party applicazione) e Azure AD B2C.  |        




Le classi di gestione SSO vengono specificate usando l'elemento `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` di un profilo tecnico.

## <a name="input-claims"></a>Attestazioni di input

L' `InputClaims` elemento è vuoto o assente.

## <a name="persisted-claims"></a>Attestazioni rese permanente

Le attestazioni che devono essere restituite all'applicazione o usate dalle precondizioni nei passaggi successivi devono essere archiviate nella sessione o potenziate da una lettura dal profilo dell'utente nella directory. L'uso di attestazioni salvate in modo permanente garantisce che i percorsi di autenticazione non abbiano esito negativo sulle attestazioni mancanti Per aggiungere attestazioni alla sessione, usare l'elemento `<PersistedClaims>` del profilo tecnico. Quando il provider viene usato per ripopolare la sessione, le attestazioni persistenti vengono aggiunte all'elenco delle attestazioni.

## <a name="output-claims"></a>Attestazioni di output

`<OutputClaims>` Viene utilizzato per recuperare le attestazioni dalla sessione.

## <a name="session-providers"></a>Provider di sessioni

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Come indica il nome, questo provider non esegue alcuna operazione. Può essere usato per eliminare il comportamento SSO per un profilo tecnico specifico. Il profilo `SM-Noop` tecnico seguente è incluso nello [Starter Pack per i criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

È possibile usare questo provider per archiviare le attestazioni in una sessione. A questo provider viene in genere fatto riferimento in un profilo tecnico utilizzato per la gestione di account locali e federati. Il profilo `SM-AAD` tecnico seguente è incluso nello [Starter Pack per i criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


Il profilo `SM-MFA` tecnico seguente è incluso nello [Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`per i criteri personalizzati. Questo profilo tecnico gestisce la sessione di autenticazione a più fattori.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Questo provider viene usato per disattivare la schermata "Choose Identity Provider" e disconnettersi da un provider di identità federato. Viene in genere fatto riferimento in un profilo tecnico configurato per un provider di identità federato, ad esempio Facebook o Azure Active Directory. Il profilo `SM-SocialLogin` tecnico seguente è incluso nello [Starter Pack per i criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | No | Non attualmente in uso, può essere ignorato. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Questo provider viene usato per gestire le sessioni di Azure AD B2C tra un relying party OAuth2 o OpenId Connect e Azure AD B2C.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Questo provider viene usato per gestire le sessioni SAML Azure AD B2C tra un'applicazione relying party o un provider di identità SAML federato. Quando si utilizza il provider SSO per archiviare una sessione del `RegisterServiceProviders` provider di identità SAML, è necessario `false`impostare su. Il profilo `SM-Saml-idp` tecnico seguente viene usato dal [profilo tecnico del provider di identità SAML](saml-identity-provider-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Quando si usa il provider per archiviare la sessione SAML B2C, `RegisterServiceProviders` è necessario impostare `true`su. Per completare la disconnessione dalla sessione SAML sono necessari `SessionIndex` e `NameID`.

Il profilo `SM-Saml-issuer` tecnico seguente viene usato dal [profilo tecnico dell'autorità emittente SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione|
| --- | --- | --- |
| IncludeSessionIndex | No | Non attualmente in uso, può essere ignorato.|
| RegisterServiceProviders | No | Indica che il provider deve registrare tutti i provider di servizi SAML a cui sia stata rilasciata un'asserzione. I valori possibili sono: `true` (impostazione predefinita) o `false`.|


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure ad B2C sessione](session-overview.md).
- Informazioni su come [configurare il comportamento della sessione nei criteri personalizzati](session-behavior-custom-policy.md).
