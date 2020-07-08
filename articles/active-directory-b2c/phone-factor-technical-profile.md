---
title: Definire un profilo tecnico del fattore di telefono in un criterio personalizzato
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
ms.openlocfilehash: 10d5fda526c41704381bb544bdfd0589063c1d15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203861"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico del fattore di telefono in un Azure Active Directory B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per la registrazione e la verifica dei numeri di telefono. Questo profilo tecnico:

- Fornisce un'interfaccia utente per interagire con l'utente per verificare o registrare un numero di telefono.
- Supporta le chiamate telefoniche e i messaggi di testo per convalidare il numero di telefono.
- Supporta più numeri di telefono. L'utente può selezionare uno dei numeri di telefono da verificare.  
- Restituisce un'attestazione che indica se l'utente ha fornito un nuovo numero di telefono. È possibile utilizzare questa attestazione per decidere se il numero di telefono deve essere salvato in modo permanente nel profilo utente Azure AD B2C.  
- Usa una [definizione di contenuto](contentdefinitions.md) per controllare l'aspetto.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **handler** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure ad B2C per il fattore di telefono:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Nell'esempio seguente viene illustrato un profilo tecnico del fattore telefonico per la registrazione e la convalida:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Trasformazioni di attestazioni di input

L'elemento InputClaimsTransformations può contenere una raccolta di trasformazioni di attestazioni di input utilizzate per modificare le attestazioni di input o per generarne di nuove. La trasformazione delle attestazioni di input seguente genera un' `UserId` attestazione che viene utilizzata in un secondo momento nella raccolta di attestazioni di input.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Attestazioni di input

L'elemento InputClaims deve contenere le attestazioni seguenti. È anche possibile mappare il nome dell'attestazione al nome definito nel profilo tecnico del fattore telefonico. 

|  Tipo di dati| Obbligatoria | Descrizione |
| --------- | -------- | ----------- | 
| string| Sì | Identificatore univoco per l'utente. Il nome dell'attestazione o PartnerClaimType deve essere impostato su `UserId` . Questa attestazione non deve contenere informazioni personali.|
| string| Sì | Elenco dei tipi di attestazione. Ogni attestazione contiene un numero di telefono. Se una delle attestazioni di input non contiene un numero di telefono, all'utente verrà richiesto di registrare e verificare un nuovo numero di telefono. Il numero di telefono convalidato viene restituito come attestazione di output. Se una delle attestazioni di input contiene un numero di telefono, viene richiesto all'utente di verificarlo. Se più attestazioni di input contengono un numero di telefono, all'utente viene richiesto di scegliere e verificare uno dei numeri di telefono. |

Nell'esempio seguente viene illustrato l'utilizzo di più numeri di telefono. Per ulteriori informazioni, vedere [criteri di esempio](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Attestazioni di output

L'elemento OutputClaims contiene un elenco di attestazioni restituite dal profilo tecnico Phone Factor.

|  Tipo di dati| Obbligatoria | Descrizione |
|  -------- | ----------- |----------- |
| boolean | Sì | Indica se il nuovo numero di telefono è stato immesso dall'utente. Il nome dell'attestazione o PartnerClaimType deve essere impostato su`newPhoneNumberEntered`|
| string| Sì | Numero di telefono verificato. Il nome dell'attestazione o PartnerClaimType deve essere impostato su `Verified.OfficePhone` .|

L'elemento OutputClaimsTransformations può contenere una raccolta di elementi OutputClaimsTransformation usati per modificare le attestazioni di output o generarne di nuovi.

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento **CryptographicKeys** non viene usato.


## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sì | Identificatore della [definizione di contenuto](contentdefinitions.md) associata a questo profilo tecnico. |
| ManualPhoneNumberEntryAllowed| No | Specificare se un utente è autorizzato o meno a immettere manualmente un numero di telefono. Valori possibili: `true` o `false` (impostazione predefinita).|
| impostazione di. authenticationMode | No | Metodo per convalidare il numero di telefono. Valori possibili: `sms` , `phone` o `mixed` (impostazione predefinita).|
| impostazione. Autodial| No| Specificare se il profilo tecnico deve comporre automaticamente o inviare automaticamente un SMS. Valori possibili: `true` o `false` (impostazione predefinita). Per la composizione automatica `setting.authenticationMode` è necessario che i metadati siano impostati su `sms` o `phone` . La raccolta di attestazioni di input deve avere un solo numero di telefono. |

### <a name="ui-elements"></a>Elementi dell'interfaccia utente

È possibile [localizzare](localization-string-ids.md#azure-mfa-error-messages)gli elementi dell'interfaccia utente della pagina di autenticazione del telefono.

## <a name="next-steps"></a>Passaggi successivi

- Controllare gli [account social e locali con](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) l'Starter Pack per l'autenticazione a più fattori.
