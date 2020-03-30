---
title: Definire un profilo tecnico di un fattore di telefono in un criterio personalizzatoDefine a phone factor technical profile in a custom policy
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico del fattore di telefono in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332660"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico del fattore di telefono in un criterio personalizzato B2C di Azure Active DirectoryDefine a phone factor technical profile in an Azure Active Directory B2C custom policy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce supporto per la registrazione e la verifica dei numeri di telefono. Questo profilo tecnico:

- Fornisce un'interfaccia utente per interagire con l'utente.
- Utilizza la definizione del contenuto per controllare l'aspetto.
- Supporta sia le telefonate che i messaggi di testo per convalidare il numero di telefono.
- Supporta più numeri di telefono. L'utente può selezionare uno dei numeri di telefono da verificare.  
- Se viene fornito un numero di telefono, l'interfaccia utente del fattore di telefono chiede all'utente di verificare il numero di telefono. Se non viene specificato, chiede all'utente di registrare un nuovo numero di telefono.
- Restituisce un'attestazione che indica se l'utente ha fornito un nuovo numero di telefono. È possibile usare questa attestazione per decidere se il numero di telefono deve essere reso persistente nel profilo utente di Azure AD.  

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **del gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C per il fattore di telefono:The handler attribute must contain the fully complete name of the protocol handler assembly that is used by Azure AD B2C for phone factor:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

L'esempio seguente mostra un profilo tecnico del fattore di telefono per la registrazione e la convalida:The following example shows a phone factor technical profile for enrollment and validation:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Attestazioni di input

L'elemento InputClaims deve contenere attestazioni seguenti. È inoltre possibile mappare il nome della richiesta al nome definito nel profilo tecnico del fattore di telefono. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

Nell'esempio seguente viene illustrato l'utilizzo di più numeri di telefono. Per ulteriori informazioni, vedere [Criteri di esempio](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

L'elemento InputClaimsTransformations può contenere una raccolta di elementi InputClaimsTransformation usati per modificare le attestazioni di input o generarne di nuove prima di presentarle alla pagina del fattore di telefono.

## <a name="output-claims"></a>Attestazioni di output

L'elemento OutputClaims contiene un elenco di attestazioni restituite dal profilo tecnico del fattore di telefono.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

L'elemento OutputClaimsTransformations può contenere una raccolta di elementi OutputClaimsTransformation che vengono usati per modificare le attestazioni di output o per generarne di nuove.

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento **CryptographicKeys** non viene usato.


## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sì | Identificatore della [definizione di contenuto](contentdefinitions.md) associata a questo profilo tecnico. |
| ManualPhoneNumberEntryAllowed| No | Specificare se un utente può immettere manualmente un numero di telefono. Valori `true` possibili: `false` o (predefinito).|

### <a name="ui-elements"></a>Elementi dell'interfaccia utente

Gli elementi dell'interfaccia utente della pagina di autenticazione del fattore di telefono possono essere [localizzati.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Passaggi successivi

- Controlla gli account social e locali con il pacchetto di avviamento [MFA.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)

