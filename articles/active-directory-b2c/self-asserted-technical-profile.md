---
title: Definire un profilo tecnico autocertificato nei criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Definire un profilo tecnico autocertificato nei criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c728660f1a77c02f1e4b5fdeb467a7dbba4e36a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512650"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico autocertificato nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tutte le interazioni in Azure Active Directory (Azure AD) B2C in cui è previsto che l'utente immetta un input sono profili tecnici autocertificati, ad esempio una pagina di registrazione, una pagina di accesso o una pagina di reimpostazione della password.

## <a name="protocol"></a>Protocol

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **handler** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C per l'autocertificazione: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

L'esempio seguente illustra un profilo tecnico autocertificato per l'iscrizione alla posta elettronica:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>Attestazioni di input

In un profilo tecnico autocertificato è possibile usare gli elementi **InputClaims** e **InputClaimsTransformations** per popolare automaticamente il valore delle attestazioni che vengono visualizzate nella pagina autocertificata (attestazioni di output). Nei criteri del profilo di modifica ad esempio il percorso utente legge prima il profilo utente dal servizio directory di Azure AD B2C, quindi il profilo tecnico autocertificato imposta le attestazioni di input con i dati utente archiviati nel profilo utente. Queste attestazioni vengono raccolte dal profilo utente e successivamente presentate all'utente che può quindi modificare i dati esistenti.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni da presentare per raccogliere i dati dall'utente. Per popolare automaticamente le attestazioni di output con alcuni valori, usare le attestazioni di input precedentemente descritte. L'elemento può contenere anche un valore predefinito. L'ordine delle attestazioni in **OutputClaims** controlla l'ordine in cui Azure AD B2C esegue il rendering delle attestazioni sullo schermo. L'attributo **DefaultValue** diventa effettivo solo se l'attestazione non è mai stata impostata prima. Se invece l'attestazione è stata impostata in un passaggio di orchestrazione precedente, anche se l'utente lascia vuoto il valore, il valore predefinito non diventa effettivo. Per forzare l'utilizzo di un valore predefinito, impostare l'attributo **AlwaysUseDefaultValue** su `true`. Per forzare l'utente a specificare un valore per un'attestazione di output specifica, impostare l'attributo **Required** dell'elemento **OutputClaims** su `true`.

L'elemento **ClaimType** nella raccolta di **OutputClaims** deve impostare l'elemento **UserInputType** su un qualsiasi tipo di input utente supportato da Azure AD B2C, ad esempio `TextBox` o `DropdownSingleSelect`. In alternativa, l'elemento **OutputClaim** deve impostare un attributo **DefaultValue**.  

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

L'attestazione di output seguente è sempre impostata su `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Caso d'uso

Esistono quattro scenari di attestazioni di output:

- **Raccolta di attestazioni di output dall'utente** - Quando occorre raccogliere informazioni dall'utente, ad esempio la data di nascita, è consigliabile aggiungere l'attestazione alla raccolta di **OutputClaims**. Le attestazioni che vengono presentate all'utente devono specificare l'attributo **UserInputType**, ad esempio `TextBox` o `DropdownSingleSelect`. Se il profilo tecnico autocertificato contiene un profilo tecnico di convalida che genera la stessa attestazione, Azure AD B2C non visualizza l'attestazione all'utente. Se non è presente alcuna attestazione di output da visualizzare all'utente, Azure AD B2C ignora il profilo tecnico.
- **Impostazione di un valore predefinito in un'attestazione di output** - Senza raccogliere i dati dall'utente o restituire i dati dal profilo tecnico di convalida. Il profilo tecnico autocertificato `LocalAccountSignUpWithLogonEmail` imposta l'attestazione **executed-SelfAsserted-Input** su `true`.
- **Un profilo tecnico di convalida restituisce le attestazioni di output** - Il profilo tecnico può chiamare un profilo tecnico di convalida che restituisce alcune attestazioni. È possibile che si desideri sviluppare le attestazioni e restituirle ai successivi passaggi dell'orchestrazione nel percorso utente. Quando si accede con un account locale, ad esempio, il profilo tecnico autocertificato denominato `SelfAsserted-LocalAccountSignin-Email` chiama il profilo tecnico di convalida denominato `login-NonInteractive`. Questo profilo tecnico convalida le credenziali utente e restituisce il profilo utente, ad esempio 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- **Output delle attestazioni tramite trasformazione delle attestazioni di output**

Nell'esempio seguente il profilo tecnico autocertificato `LocalAccountSignUpWithLogonEmail` dimostra l'utilizzo delle attestazioni di output e imposta **executed-SelfAsserted-Input** su `true`. Le attestazioni `objectId`, `authenticationSource` e `newUser` sono output del profilo tecnico di convalida `AAD-UserWriteUsingLogonEmail` e non vengono visualizzate all'utente.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Rendere persistenti le attestazioni

Se l'elemento **PersistedClaims** è assente, il profilo tecnico autocertificato non rende persistenti i dati ad Azure AD B2C. Viene invece eseguita una chiamata a un profilo tecnico di convalida che è responsabile della persistenza dei dati. I criteri di accesso ad esempio usano il profilo tecnico autocertificato `LocalAccountSignUpWithLogonEmail` per raccogliere il nuovo profilo utente. Il profilo tecnico `LocalAccountSignUpWithLogonEmail` chiama il profilo tecnico di convalida per creare l'account in Azure AD B2C.

## <a name="validation-technical-profiles"></a>Profili tecnici di convalida

Il profilo tecnico di convalida viene usato per convalidare alcune o tutte le attestazioni di output del profilo tecnico di riferimento. Le attestazioni di input del profilo tecnico di convalida devono apparire nelle attestazioni di output del profilo tecnico autocertificato. Il profilo tecnico di convalida esegue la convalida dell'input utente e può restituire un errore all'utente. 

Il profilo tecnico di convalida può essere un qualsiasi profilo tecnico nei criteri, ad esempio un profilo tecnico di [Azure Active Directory](active-directory-technical-profile.md) o di un'[API REST](restful-technical-profile.md). Nell'esempio precedente il profilo tecnico `LocalAccountSignUpWithLogonEmail` convalida che l'attributo signinName non esiste nella directory. In caso contrario, il profilo tecnico di convalida crea un account locale e restituisce il valore objectId, authenticationSource, newUser. Il profilo tecnico `SelfAsserted-LocalAccountSignin-Email` chiama il profilo tecnico di convalida `login-NonInteractive` per convalidare le credenziali utente.

È possibile anche chiamare un profilo tecnico di API REST usando la logica di business che si preferisce, sovrascrivere le attestazioni di input o arricchire i dati utente integrando ulteriormente con applicazioni line-of-business aziendali. Per altre informazioni, vedere [Validation technical profile](validation-technical-profile.md) (Profilo tecnico di convalida)

## <a name="metadata"></a>Metadata

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| setting.showContinueButton | No | Visualizza il pulsante Continua. I valori possibili sono: `true` (impostazione predefinita) o `false` |
| setting.showCancelButton | No | Visualizza il pulsante Annulla. I valori possibili sono: `true` (impostazione predefinita) o `false` |
| setting.operatingMode | No | Per una pagina di accesso, questa proprietà controlla il comportamento del campo del nome utente, ad esempio i messaggi di errore e di convalida di input. I valori previsti sono: `Username` o `Email`. |
| ContentDefinitionReferenceId | Yes | Identificatore della [definizione di contenuto](contentdefinitions.md) associata a questo profilo tecnico. |
| EnforceEmailVerification | No | Per la registrazione o la modifica del profilo, applica la verifica tramite posta elettronica. I valori possibili sono: `true` (impostazione predefinita) o `false`. | 
| setting.showSignupLink | No | Visualizza il pulsante di iscrizione. I valori possibili sono: `true` (impostazione predefinita) o `false` |
| setting.retryLimit | No | Controlla il numero di volte in cui un utente può provare a specificare i dati che vengono controllati rispetto a un profilo tecnico di convalida. Un utente tenta ad esempio di iscriversi con un account che esiste già e insiste fino a quando non raggiunge il limite.
| SignUpTarget | No | Identificatore di scambio di destinazione dell'iscrizione. Quando l'utente fa clic sul pulsante di iscrizione, Azure AD B2C esegue l'identificatore di scambio specificato. |

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento **CryptographicKeys** non viene usato.













