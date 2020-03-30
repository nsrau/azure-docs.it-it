---
title: Definire un profilo tecnico di Azure AD in un criterio personalizzatoDefine an Azure AD technical profile in a custom policy
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico di Azure Active Directory in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330378"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico di Azure Active Directory in un criterio personalizzato di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per la gestione degli utenti di Azure Active Directory.Azure Active Directory B2C (Azure AD B2C) provides support for the Azure Active Directory user management. Questo articolo descrive le specifiche di un profilo tecnico per l'interazione con un provider di attestazioni che supporta questo protocollo standardizzato.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **gestore** deve contenere il nome completo dell'assembly del gestore di protocollo `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

I profili tecnici di Azure AD seguenti di criteri personalizzati includono il profilo tecnico AAD-Common.Following [custom policy starter pack](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD technical profiles include the **AAD-Common** technical profile. I profili tecnici di Azure AD non specificano il protocollo perché il protocollo è configurato nel profilo tecnico **AAD-Common:The** Azure AD technical profiles don't specify the protocol because the protocol is configured in the AAD-Common technical profile:
 
- **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingAlternativeSecurityId-NoError** - Cercare un account social nella directory.
- **AAD-UserWriteUsingAlternativeSecurityId** - Creare un nuovo account social.
- **AAD-UserReadUsingEmailAddress** - Cercare un account locale nella directory.
- **AAD-UserWriteUsingLogonEmail** - Creare un nuovo account locale.
- **AAD-UserWritePasswordUsingObjectId** - Aggiornare una password di un account locale.
- **AAD-UserWriteProfileUsingObjectId** - Aggiornare un profilo utente di un account locale o social.
- **AAD-UserReadUsingObjectId** - Leggere un profilo utente di un account locale o social.
- **AAD-UserWritePhoneNumberUsingObjectId** - Scrivere il numero di telefono di autenticazione a più fattori di un account locale o social

Nell'esempio seguente viene illustrato un profilo tecnico **AAD-Common**:

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

L'elemento InputClaims contiene un'attestazione, usata per cercare un account nella directory o crearne una nuova. Deve essere presente esattamente un elemento InputClaim nella raccolta di attestazioni di input per tutti i profili tecnici di Azure AD. Potrebbe essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri al nome definito in Azure Active Directory.

Per leggere, aggiornare o eliminare un account utente esistente, l'attestazione di input è una chiave che identifica in modo univoco l'account nella directory di Azure AD. Ad esempio, **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName**o **alternativeSecurityId**. 

Per creare un nuovo account utente, l'attestazione di input è una chiave che identifica in modo univoco un account locale o federato. Ad esempio, account locale: **signInNames.emailAddress**o **signInNames.userName**. Per un account federato: **alternativeSecurityId**.

L'elemento [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) può contenere una raccolta di elementi di trasformazione delle attestazioni di input usati per modificare l'attestazione di input o generarne una nuova.

## <a name="outputclaims"></a>OutputClaims

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dal profilo tecnico di Azure AD. Potrebbe essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri al nome definito in Azure Active Directory. È anche possibile includere le attestazioni che non vengono restituite da Azure Active Directory, fino a quando è impostato l'attributo `DefaultValue`.

L'elemento [OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

Ad esempio, il profilo tecnico **AAD-UserWriteUsingLogonEmail** crea un account locale e restituisce le attestazioni seguenti:

- **objectId**, ovvero l'identificatore del nuovo account
- **newUser**, che indica se l'utente è nuovo
- **authenticationSource**, che imposta l'autenticazione a `localAccountAuthentication`
- **userPrincipalName**, ovvero il nome dell'entità utente del nuovo account
- **signInNames.emailAddress**, ovvero il nome di accesso dell'account, simile all'attestazione input del **messaggio di posta elettronica**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

L'elemento **PersistedClaims** contiene tutti i valori che devono essere resi persistenti da Azure AD con le possibili informazioni di mapping tra un tipo di attestazione già definito nella sezione [ClaimsSchema](claimsschema.md) nei criteri e il nome dell'attributo di Azure AD.

Il profilo tecnico **AAD-UserWriteUsingLogonEmail** che crea un nuovo account locale, rende persistenti le attestazioni seguenti:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Il nome dell'attestazione è il nome dell'attributo Azure AD, a meno che non sia specificato l'attributo **PartnerClaimType** che contiene il nome dell'attributo Azure AD.

## <a name="requirements-of-an-operation"></a>Requisiti di un'operazione

- Deve essere presente esattamente un elemento **InputClaim** nell'elenco delle attestazioni per tutti i profili di tecnici di Azure AD.
- [L'articolo sugli attributi dei profili utente](user-profile-attributes.md) descrive gli attributi dei profili utente B2C di Azure AD supportati che è possibile usare nelle attestazioni di input, nelle attestazioni di output e nelle attestazioni persistenti. 
- Se l'operazione è `Write` oppure `DeleteClaims`, allora deve essere visualizzata anche in un elemento **PersistedClaims**.
- Il valore dell'attestazione **userPrincipalName** deve essere nel formato `user@tenant.onmicrosoft.com`.
- L'attestazione **displayName** è obbligatoria e non può essere una stringa vuota.

## <a name="azure-ad-technical-provider-operations"></a>Operazioni di provider tecnico di Azure AD

### <a name="read"></a>Lettura

L'operazione di **lettura** legge i dati su un singolo account utente. Il profilo tecnico seguente legge i dati su un account utente usando l'objectId dell'utente:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Scrittura

L'operazione di **scrittura** crea o aggiorna un singolo account utente. Il profilo tecnico seguente crea nuovi account social:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

L'operazione **DeleteClaims** elimina le informazioni da un elenco specificato di attestazioni. Il profilo tecnico seguente elimina le attestazioni:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

L'operazione **DeleteClaimsPrincipal** elimina un singolo account utente dalla directory. Il profilo tecnico seguente elimina un account utente dalla directory usando il nome dell'entità utente:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Il profilo tecnico seguente elimina un account utente social mediante **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | L'operazione da eseguire. I valori possibili sono: `Read`, `Write`, `DeleteClaims` o `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | No | Genera un errore se l'oggetto utente non esiste nella directory. I valori possibili sono: `true` o `false`. |
| RaiseErrorIfClaimsPrincipalAlreadyExists | No | Genera un errore se l'oggetto utente esiste già. I valori possibili sono: `true` o `false`.|
| ApplicationObjectId | No | L'identificatore di oggetto dell'applicazione per gli attributi di estensione. Valore: ObjectID di un'applicazione. Per ulteriori informazioni, consultate Utilizzare attributi personalizzati in un criterio di [modifica del profilo personalizzato.](custom-policy-custom-attributes.md) |
| ClientId | No | L'identificatore client per l'accesso ai tenant come terza parte. Per altre informazioni, vedere [Usare gli attributi personalizzati in un criterio di modifica del profilo personalizzato](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | No | Per le attestazioni di input e output, specifica se la [risoluzione delle attestazioni](claim-resolver-overview.md) è inclusa nel profilo tecnico. Valori possibili: `true` `false`  , o (impostazione predefinita). Se si desidera utilizzare un resolver di attestazioni `true`nel profilo tecnico, impostarlo su . |

### <a name="ui-elements"></a>Elementi dell'interfaccia utente
 
Le impostazioni seguenti possono essere utilizzate per configurare il messaggio di errore visualizzato in caso di errore. I metadati devono essere configurati nel profilo tecnico [auto-asserito.](self-asserted-technical-profile.md) I messaggi di errore possono essere [localizzati.](localization.md)

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | No | Se deve essere generato un errore (vedere la descrizione dell'attributo RaiseErrorIfClaimsPrincipalAlreadyExists) specificare il messaggio da visualizzare all'utente se l'oggetto utente esiste già. |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Se deve essere generato un errore (vedere la descrizione dell'attributo RaiseErrorIfClaimsPrincipalDoesNotExist), specificare il messaggio da visualizzare all'utente se l'oggetto utente non esiste. |


## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente, ad esempio, per usare il profilo tecnico di Azure AD:See the following article, for example of using Azure AD technical profile:

- [Aggiungere attestazioni e input di personalizzazione dell'esperienza utente tramite criteri personalizzati in Azure Active Directory B2C](custom-policy-configure-user-input.md)














