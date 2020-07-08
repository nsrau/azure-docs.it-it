---
title: Gestione delle sessioni Single Sign-On con criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni sulla gestione delle sessioni SSO con criteri personalizzati in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4824b64236270c422f22809e9eeb191ee3be27fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85202569"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestione delle sessioni Single Sign-On in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Per la gestione delle [sessioni Single Sign-On (SSO)](session-overview.md) si usa la stessa semantica di qualsiasi altro profilo tecnico nei criteri personalizzati. Quando si esegue un passaggio di orchestrazione, nel profilo tecnico associato al passaggio viene eseguita la query di un riferimento a `UseTechnicalProfileForSessionManagement`. Se ne esiste uno, il provider della sessione SSO a cui si fa riferimento viene controllato per verificare se l'utente sia un partecipante alla sessione. In caso affermativo, il provider della sessione SSO viene usato per ripopolare la sessione. In modo analogo, al termine dell'esecuzione di un passaggio di orchestrazione, il provider viene usato per archiviare le informazioni nella sessione, se è stato specificato un provider di sessione SSO.

In Azure AD B2C sono stati definiti alcuni provider di sessione SSO che è possibile usare:

|Provider di sessione  |Ambito  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  nessuno       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Strumento di gestione interno di Azure AD B2C.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Tra Azure AD B2C e uno dei provider di identità OAuth1, OAuth2 oppure OpenId Connect.        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Tra un'applicazione relying party OAuth2 oppure OpenId Connect e Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Tra Azure AD B2C e il provider di identità SAML, nonché tra un provider di servizi SAML (applicazione relying party) e Azure AD B2C.  |        




Le classi di gestione SSO vengono specificate usando l'elemento `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` di un profilo tecnico.

## <a name="input-claims"></a>Attestazioni di input

L'elemento `InputClaims` è vuoto o assente.

## <a name="persisted-claims"></a>Attestazioni persistenti

Le attestazioni che devono essere restituite all'applicazione o usate dalle precondizioni nei passaggi successivi devono essere archiviate nella sessione o potenziate da una lettura dal profilo dell'utente nella directory. L'uso di attestazioni persistenti garantisce che non si verifichino errori dei percorsi di autenticazione non in caso di attestazioni mancanti. Per aggiungere attestazioni alla sessione, usare l'elemento `<PersistedClaims>` del profilo tecnico. Quando il provider viene usato per ripopolare la sessione, le attestazioni persistenti vengono aggiunte all'elenco delle attestazioni.

## <a name="output-claims"></a>Attestazioni di output

`<OutputClaims>` viene usato per recuperare le attestazioni dalla sessione.

## <a name="session-providers"></a>Provider di sessioni

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Come indica il nome, questo provider non esegue alcuna operazione. Può essere usato per eliminare il comportamento SSO per un profilo tecnico specifico. Il profilo tecnico `SM-Noop` seguente è incluso nel [pacchetto Starter dei criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

È possibile usare questo provider per archiviare le attestazioni in una sessione. Un riferimento a questo provider è in genere incluso in un profilo tecnico usato per gestire account locali e federati. Il profilo tecnico `SM-AAD` seguente è incluso nel [pacchetto Starter dei criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
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


Il profilo tecnico `SM-MFA` seguente è incluso nel [pacchetto Starter dei criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).`SocialAndLocalAccountsWithMfa` Questo profilo tecnico consente di gestire la sessione di autenticazione a più fattori.

```xml
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

Questo provider viene usato per disattivare la schermata di selezione del provider di identità e disconnettersi da un provider di identità federato. Un riferimento a questo provider è in genere incluso in un profilo tecnico configurato per un provider di identità federato, ad esempio Facebook o Azure Active Directory. Il profilo tecnico `SM-SocialLogin` seguente è incluso nel [pacchetto Starter dei criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
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

| Attributo | Obbligatorio | Descrizione|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | No | Attualmente non usato; può essere ignorato. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Questo provider viene usato per gestire le sessioni di Azure AD B2C tra un relying party OAuth2 o OpenId Connect e Azure AD B2C.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Questo provider viene usato per gestire le sessioni SAML di Azure AD B2C tra un'applicazione relying party o un provider di identità SAML federato. Quando si usa il provider SSO per archiviare una sessione del provider di identità SAML, `RegisterServiceProviders` deve essere impostato su `false`. Il profilo tecnico `SM-Saml-idp` seguente viene usato dal [profilo tecnico del provider di identità SAML](saml-identity-provider-technical-profile.md).

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Quando si usa il provider per archiviare la sessione SAML B2C, `RegisterServiceProviders` deve essere impostato su `true`. Per completare la disconnessione dalla sessione SAML sono necessari `SessionIndex` e `NameID`.

Il profilo tecnico `SM-Saml-issuer` seguente viene usato dal [profilo tecnico dell'autorità di certificazione SAML](saml-issuer-technical-profile.md).

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadati

| Attributo | Obbligatorio | Descrizione|
| --- | --- | --- |
| IncludeSessionIndex | No | Attualmente non usato; può essere ignorato.|
| RegisterServiceProviders | No | Indica che il provider deve registrare tutti i provider di servizi SAML a cui sia stata rilasciata un'asserzione. I valori possibili sono: `true` (impostazione predefinita) o `false`.|


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [sessione Azure AD B2C](session-overview.md).
- Informazioni su come [configurare il comportamento delle sessioni in criteri personalizzati](session-behavior-custom-policy.md).
