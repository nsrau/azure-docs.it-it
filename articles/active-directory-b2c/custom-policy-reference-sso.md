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
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79246032"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestione delle sessioni Single Sign-On in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

La gestione delle sessioni Single Sign-on (SSO) in Azure Active Directory B2C (Azure AD B2C) consente a un amministratore di controllare l'interazione con un utente dopo che l'utente è già stato autenticato. L'amministratore può ad esempio controllare se verrà visualizzata la selezione dei provider di identità o se sarà necessario immettere nuovamente i dettagli dell'account locale. In questo articolo viene descritto come configurare le impostazioni di SSO per Azure AD B2C.

La gestione della sessione SSO è costituita da due parti. La prima riguarda le interazioni dell'utente direttamente con Azure AD B2C, mentre l'altra riguarda le interazioni dell'utente con parti esterne, ad esempio Facebook. Azure AD B2C non esegue l'override né ignora le sessioni SSO che possono essere tenute da parti esterne. Il percorso attraverso Azure AD B2C per passare alla parte esterna viene invece "memorizzato", evitando di dover richiedere all'utente di selezionare il provider di identità social o aziendale. La decisione SSO finale spetta alla parte esterna.

La gestione della sessione SSO usa la stessa semantica di qualsiasi altro profilo tecnico nei criteri personalizzati. Quando si esegue un passaggio di orchestrazione, nel profilo tecnico associato al passaggio viene eseguita la query di un riferimento a `UseTechnicalProfileForSessionManagement`. Se ne esiste uno, il provider della sessione SSO a cui si fa riferimento viene controllato per verificare se l'utente sia un partecipante alla sessione. In caso affermativo, il provider della sessione SSO viene usato per ripopolare la sessione. In modo analogo, al termine dell'esecuzione di un passaggio di orchestrazione, il provider viene usato per archiviare le informazioni nella sessione, se è stato specificato un provider di sessione SSO.

In Azure AD B2C sono stati definiti alcuni provider di sessione SSO che è possibile usare:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

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

È possibile usare questo provider per archiviare le attestazioni in una sessione. Un riferimento a questo provider è in genere incluso in un profilo tecnico usato per gestire account locali. Il profilo `SM-AAD` tecnico seguente è incluso nello [Starter Pack per i criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

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

Questo provider viene usato per disattivare la schermata "Choose Identity Provider". Un riferimento a questo provider è in genere incluso in un profilo tecnico configurato per un provider di identità esterno, ad esempio Facebook. Il profilo `SM-SocialLogin` tecnico seguente è incluso nello [Starter Pack per i criteri personalizzati](custom-policy-get-started.md#custom-policy-starter-pack).

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
| AlwaysFetchClaimsFromProvider | No | Non attualmente in uso, può essere ignorato. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Questo provider viene usato per gestire le sessioni SAML Azure AD B2C tra un'applicazione relying party o un provider di identità SAML federato. Quando si utilizza il provider SSO per archiviare una sessione del `RegisterServiceProviders` provider di identità SAML, è necessario `false`impostare su. Il profilo `SM-Saml-idp` tecnico seguente viene utilizzato dal [profilo tecnico SAML](saml-technical-profile.md).

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

Il profilo `SM-Saml-idp` tecnico seguente viene usato dal [profilo tecnico dell'autorità emittente SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione|
| --- | --- | --- |
| IncludeSessionIndex | No | Non attualmente in uso, può essere ignorato.|
| RegisterServiceProviders | No | Indica che il provider deve registrare tutti i provider di servizi SAML a cui sia stata rilasciata un'asserzione. I valori possibili sono: `true` (impostazione predefinita) o `false`.|



