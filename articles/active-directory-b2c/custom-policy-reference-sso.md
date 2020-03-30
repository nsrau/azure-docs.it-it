---
title: Gestione delle sessioni Single Sign-On con criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni sulla gestione delle sessioni SSO tramite criteri personalizzati in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246032"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestione delle sessioni Single Sign-On in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

La gestione della sessione Single Sign-On (SSO) in Azure Active Directory B2C (Azure AD B2C) consente a un amministratore di controllare l'interazione con un utente dopo che l'utente ha già eseguito l'autenticazione. L'amministratore può ad esempio controllare se verrà visualizzata la selezione dei provider di identità o se sarà necessario immettere nuovamente i dettagli dell'account locale. In questo articolo viene descritto come configurare le impostazioni di SSO per Azure AD B2C.

La gestione della sessione SSO è costituita da due parti. La prima riguarda le interazioni dell'utente direttamente con Azure AD B2C, mentre l'altra riguarda le interazioni dell'utente con parti esterne, ad esempio Facebook. Azure AD B2C non esegue l'override né ignora le sessioni SSO che possono essere tenute da parti esterne. Piuttosto, la route tramite Azure AD B2C per raggiungere l'entità esterna viene "ricordata", evitando la necessità di richiedere nuovamente all'utente di selezionare il provider di identità aziendale o di social networking. La decisione SSO finale spetta alla parte esterna.

La gestione della sessione SSO usa la stessa semantica di qualsiasi altro profilo tecnico nei criteri personalizzati. Quando si esegue un passaggio di orchestrazione, nel profilo tecnico associato al passaggio viene eseguita la query di un riferimento a `UseTechnicalProfileForSessionManagement`. Se ne esiste uno, il provider della sessione SSO a cui si fa riferimento viene controllato per verificare se l'utente sia un partecipante alla sessione. In caso affermativo, il provider della sessione SSO viene usato per ripopolare la sessione. In modo analogo, al termine dell'esecuzione di un passaggio di orchestrazione, il provider viene usato per archiviare le informazioni nella sessione, se è stato specificato un provider di sessione SSO.

In Azure AD B2C sono stati definiti alcuni provider di sessione SSO che è possibile usare:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Le classi di gestione SSO vengono specificate usando l'elemento `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` di un profilo tecnico.

## <a name="input-claims"></a>Attestazioni di input

L'elemento `InputClaims` è vuoto o assente.

## <a name="persisted-claims"></a>Attestazioni persistentiPersisted claims

Le attestazioni che devono essere restituite all'applicazione o utilizzate da precondizioni nei passaggi successivi, devono essere archiviate nella sessione o aumentate da una lettura dal profilo dell'utente nella directory. L'uso di attestazioni persistenti garantisce che i percorsi di autenticazione non avranno esito negativo nelle attestazioni mancanti. Per aggiungere attestazioni alla sessione, usare l'elemento `<PersistedClaims>` del profilo tecnico. Quando il provider viene usato per ripopolare la sessione, le attestazioni persistenti vengono aggiunte all'elenco delle attestazioni.

## <a name="output-claims"></a>Attestazioni di output

L'oggetto `<OutputClaims>` viene utilizzato per il recupero di attestazioni dalla sessione.

## <a name="session-providers"></a>Provider di sessione

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Come indica il nome, questo provider non esegue alcuna operazione. Può essere usato per eliminare il comportamento SSO per un profilo tecnico specifico. Il `SM-Noop` seguente profilo tecnico è incluso nello [starter pack dei criteri personalizzati.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

È possibile usare questo provider per archiviare le attestazioni in una sessione. Un riferimento a questo provider è in genere incluso in un profilo tecnico usato per gestire account locali. Il `SM-AAD` seguente profilo tecnico è incluso nello [starter pack dei criteri personalizzati.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
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

Il `SM-MFA` seguente profilo tecnico è incluso nello [starter pack](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`dei criteri personalizzati. Questo profilo tecnico gestisce la sessione di autenticazione a più fattori.

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

Questo provider viene utilizzato per eliminare la schermata "scegli provider di identità". Un riferimento a questo provider è in genere incluso in un profilo tecnico configurato per un provider di identità esterno, ad esempio Facebook. Il `SM-SocialLogin` seguente profilo tecnico è incluso nello [starter pack dei criteri personalizzati.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
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
| AlwaysFetchClaimsFromProviderAlwaysFetchClaimsFromProvider | No | Non attualmente utilizzato, può essere ignorato. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Questo provider viene utilizzato per la gestione delle sessioni SAML B2C di Azure AD tra un'applicazione relying party o un provider di identità SAML federato. Quando si utilizza il provider SSO per l'archiviazione di una sessione del provider di identità SAML, è necessario impostare su `RegisterServiceProviders` `false`. Il `SM-Saml-idp` seguente profilo tecnico viene utilizzato dal [profilo tecnico SAML.](saml-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Quando si utilizza il provider per l'archiviazione `RegisterServiceProviders` della `true`sessione SAML B2C, è necessario impostare su . Per completare la disconnessione dalla sessione SAML sono necessari `SessionIndex` e `NameID`.

Il `SM-Saml-idp` seguente profilo tecnico viene utilizzato dal profilo tecnico dell'emittente [SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione|
| --- | --- | --- |
| IncludeSessionIndex | No | Non attualmente utilizzato, può essere ignorato.|
| RegisterServiceProviders | No | Indica che il provider deve registrare tutti i provider di servizi SAML a cui sia stata rilasciata un'asserzione. I valori possibili sono: `true` (impostazione predefinita) o `false`.|



