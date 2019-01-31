---
title: Definire un profilo tecnico di trasformazione delle attestazioni in un criterio personalizzato di Azure Active Directory B2C | Microsoft Docs
description: Definire un profilo tecnico di trasformazione delle attestazioni in un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a204e8cdc20a6897c40d4d5f68217a2922371737
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148609"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico di trasformazione delle attestazioni in un criterio personalizzato di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profilo tecnico di trasformazione delle attestazioni consente di chiamare le trasformazioni delle attestazioni di output, modificare i valori delle attestazioni, convalidare le attestazioni o impostare i valori predefiniti per un set di attestazioni di output.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Nell'esempio seguente viene illustrato un profilo tecnico di trasformazione delle attestazioni:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** è obbligatorio. È necessario fornire almeno un'attestazione di output restituita dal profilo tecnico. Nell'esempio seguente viene illustrato come impostare i valori predefiniti nelle attestazioni di output:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Trasformazioni delle attestazioni di output

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi**OutputClaimsTransformation** che vengono usati per modificare le attestazioni o generarne di nuove. Il seguente profilo tecnico chiama la trasformazione delle attestazioni **RemoveAlternativeSecurityIdByIdentityProvider**. Questa trasformazione delle attestazioni elimina un identificatore sociale dalla raccolta di **AlternativeSecurityIds**. Le attestazioni di output di questo profilo tecnico sono **identityProvider2**, impostate su `facebook.com`, e **AlternativeSecurityIds**, che contiene l'elenco delle identità sociali associate a questo utente, una volta che l'identità facebook.com è stata eliminata.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Il profilo tecnico di trasformazione delle attestazioni consente di eseguire una trasformazione delle attestazioni dal passaggio di orchestrazione di qualsiasi percorso utente. Nell'esempio seguente, il passaggio di orchestrazione chiama uno dei profili tecnici di scollegamento, ad esempio **UnLink-Facebook-OAUTH**. Questo profilo tecnico chiama il profilo tecnico di trasformazione delle attestazioni **RemoveAlternativeSecurityIdByIdentityProvider**, che genera una nuova attestazione **AlternativeSecurityIds2** contenente l'elenco delle identità sociali dell'utente, mentre elimina le identità di Facebook dalle raccolte.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="use-a-validation-technical-profile"></a>Usare un profilo tecnico di convalida

Si può usare un profilo tecnico di trasformazione delle attestazioni per convalidare le informazioni. Nell'esempio seguente, il [profilo tecnico autocertificato](self-asserted-technical-profile.md) denominato **LocalAccountSignUpWithLogonEmail** richiede all'utente di immettere due volte l'e-mail, quindi chiama il [profilo tecnico di convalida](validation-technical-profile.md) denominato **Convalida-email** per convalidare le e-mail. Il profilo tecnico**Convalida-email** chiama la trasformazione delle attestazioni **AssertEmailAreEqual** per confrontare le due attestazioni **email** e **emailRepeat**e genera un'eccezione se non sono uguali in base al confronto specificato.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Il profilo tecnico di trasformazione delle attestazioni chiama la trasformazione delle attestazioni **AssertEmailAreEqual** secondo cui le e-mail fornite dall'utente sono uguali.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Un profilo tecnico autocertificato può chiamare il profilo tecnico di convalida e visualizzare il messaggio di errore come specificato nei metadati **UserMessageIfClaimsTransformationStringsAreNotEqual**.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
