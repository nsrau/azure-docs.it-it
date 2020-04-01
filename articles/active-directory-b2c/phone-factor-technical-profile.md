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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437458"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico del fattore di telefono in un criterio personalizzato B2C di Azure Active DirectoryDefine a phone factor technical profile in an Azure Active Directory B2C custom policy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce supporto per la registrazione e la verifica dei numeri di telefono. Questo profilo tecnico:

- Fornisce un'interfaccia utente per interagire con l'utente per verificare o registrare un numero di telefono.
- Supporta telefonate e messaggi di testo per convalidare il numero di telefono.
- Supporta più numeri di telefono. L'utente può selezionare uno dei numeri di telefono da verificare.  
- Restituisce un'attestazione che indica se l'utente ha fornito un nuovo numero di telefono. È possibile usare questa attestazione per decidere se il numero di telefono deve essere reso persistente nel profilo utente B2C di Azure AD.  
- Utilizza una [definizione](contentdefinitions.md) di contenuto per controllare l'aspetto.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **del gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C per il fattore di telefono:The handler attribute must contain the fully complete name of the protocol handler assembly that is used by Azure AD B2C for phone factor:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

L'esempio seguente mostra un profilo tecnico del fattore di telefono per la registrazione e la convalida:The following example shows a phone factor technical profile for enrollment and validation:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Trasformazioni delle attestazioni di inputInput claims transformations

L'elemento InputClaimsTransformations può contenere una raccolta di trasformazioni di attestazioni di input usate per modificare le attestazioni di input o generarne di nuove. La trasformazione delle `UserId` attestazioni di input seguente genera un'attestazione che viene usata in un secondo momento nella raccolta di attestazioni di input.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Attestazioni di input

L'elemento InputClaims deve contenere le attestazioni seguenti. È inoltre possibile mappare il nome della richiesta al nome definito nel profilo tecnico del fattore di telefono. 

|  Tipo di dati| Obbligatoria | Descrizione |
| --------- | -------- | ----------- | 
| string| Sì | Identificatore univoco per l'utente. Il nome dell'attestazione o PartnerClaimType deve essere impostato su `UserId`. Questa affermazione non deve contenere informazioni personali identificabili.|
| string| Sì | Elenco dei tipi di attestazione. Ogni reclamo contiene un numero di telefono. Se una delle attestazioni di input non contiene un numero di telefono, all'utente verrà chiesto di registrarsi e verificare un nuovo numero di telefono. Il numero di telefono convalidato viene restituito come attestazione di output. Se una delle attestazioni di input contiene un numero di telefono, all'utente viene chiesto di verificarlo. Se più attestazioni di input contengono un numero di telefono, all'utente viene chiesto di scegliere e verificare uno dei numeri di telefono. |

Nell'esempio seguente viene illustrato l'utilizzo di più numeri di telefono. Per ulteriori informazioni, vedere [Criteri di esempio](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Attestazioni di output

L'elemento OutputClaims contiene un elenco di attestazioni restituite dal profilo tecnico del fattore di telefono.

|  Tipo di dati| Obbligatoria | Descrizione |
|  -------- | ----------- |----------- |
| boolean | Sì | Indica se il nuovo numero di telefono è stato immesso dall'utente. Il nome dell'attestazione o PartnerClaimType deve essere impostato su`newPhoneNumberEntered`|
| string| Sì | Numero di telefono verificato. Il nome dell'attestazione o PartnerClaimType deve essere impostato su `Verified.OfficePhone`.|

Il OutputClaimsTransformations elemento può contenere una raccolta di OutputClaimsTransformation elementi che vengono utilizzati per modificare le attestazioni di output o generare nuovi.

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento **CryptographicKeys** non viene usato.


## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sì | Identificatore della [definizione di contenuto](contentdefinitions.md) associata a questo profilo tecnico. |
| ManualPhoneNumberEntryAllowed| No | Specificare se un utente può immettere manualmente un numero di telefono. Valori possibili: `true` `false` , o (impostazione predefinita).|
| setting.authenticationMode (modalità impostazione) | No | Metodo per convalidare il numero di telefono. Valori `sms`possibili: `phone`, `mixed` , o (impostazione predefinita).|
| setting.autodial| No| Specificare se il profilo tecnico deve comporre automaticamente o inviare automaticamente un SMS. Valori possibili: `true` `false` , o (impostazione predefinita). La composizione automatica `setting.authenticationMode` richiede che `sms`i `phone`metadati siano impostati su , o . La raccolta di attestazioni di input deve avere un singolo numero di telefono. |

### <a name="ui-elements"></a>Elementi dell'interfaccia utente

Gli elementi dell'interfaccia utente della pagina di autenticazione del fattore di telefono possono essere [localizzati.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Passaggi successivi

- Controlla gli account social e locali con il pacchetto di avviamento [MFA.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)
