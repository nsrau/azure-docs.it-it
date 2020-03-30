---
title: Trasformazioni delle attestazioni del numero di telefono nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni di riferimento sui criteri personalizzati per le trasformazioni delle attestazioni dei numeri di telefono in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183933"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definire le trasformazioni delle attestazioni del numero di telefono in Azure AD B2CDefine phone number claims transformations in Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce informazioni di riferimento ed esempi per l'uso delle trasformazioni delle attestazioni del numero di telefono dello schema Identity Experience Framework in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni sulle trasformazioni delle attestazioni in generale, vedere [ClaimsTransformations.For](claimstransformations.md)more information about claims transformations in general, see ClaimsTransformations .

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Converte `phoneNumber` un tipo `string` di dati in un tipo di dati.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  Il ClaimType da convertire in una stringa. |
| OutputClaim | PhoneNumberString | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

In questo esempio, l'attestazione cellPhoneNumber con un tipo di valore `phoneNumber` `string`di viene convertita in un'attestazione cellPhone con un tipo di valore di .

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **phoneNumber**: 11234567890 (numeroditelefono)
- Attestazioni di output:
  - **phoneNumberString**: 11234567890 (stringa)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim (ConvertStringToPhoneNumberClaim)

Questa trasformazione attestazione convalida il formato del numero di telefono. Se è in un formato valido, modificarlo in un formato standard usato da Azure AD B2C. Se il numero di telefono fornito non è in un formato valido, viene restituito un messaggio di errore.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | PhoneNumberString | string |  Attestazione di stringa per il numero di telefono. Il numero di telefono deve essere in formato internazionale, completo di un leader "" e codice paese. Se viene `country` fornita l'attestazione di input, il numero di telefono è in formato locale (senza il prefisso internazionale). |
| InputClaim | country | string | [Facoltativo] La richiesta di stringa per il codice paese del numero di telefono in formato ISO3166 (il codice paese ISO-3166 di due lettere). |
| OutputClaim | outputClaim | phoneNumber | Il risultato di questa trasformazione delle attestazioni. |

La trasformazione delle attestazioni **ConvertStringToPhoneNumberClaim** viene sempre eseguita da un [profilo tecnico](validation-technical-profile.md) di convalida chiamato da un profilo tecnico o un controllo [di visualizzazione](display-controls.md) [auto-asserito.](self-asserted-technical-profile.md) I metadati del profilo tecnico **autoassedUserMessageIfClaimsTransformationInvalidPhoneNumber** controllano il messaggio di errore presentato all'utente.

![Diagramma del percorso di esecuzione dei messaggi di errore](./media/phone-authentication/assert-execution.png)

È possibile usare questa trasformazione delle attestazioni per assicurarsi che l'attestazione stringa fornita sia un numero di telefono valido. In caso contrario, viene generato un messaggio di errore. L'esempio seguente controlla che **phoneString** ClaimType sia effettivamente un numero di telefono valido e quindi restituisce il numero di telefono nel formato B2C standard di Azure AD. In caso contrario, viene generato un messaggio di errore.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Il profilo tecnico auto-asserito che chiama il profilo tecnico di convalida che contiene questa trasformazione delle attestazioni può definire il messaggio di errore.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Esempio 1

- Attestazioni di input:
  - **phoneNumberString**: 033 456-7890
  - **paese**: DK
- Attestazioni di output:
  - **outputClaim**: 450334567890

### <a name="example-2"></a>Esempio 2

- Attestazioni di input:
  - **PhoneNumberString**: 1 (123) 456-7890
- Attestazioni di output:
  - **outputClaim**: 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

In questo modo il codice paese e il numero nazionale vengono estratti dall'attestazione di input e, facoltativamente, viene generata un'eccezione se il numero di telefono fornito non è valido.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | string | Attestazione di stringa del numero di telefono. Il numero di telefono deve essere in formato internazionale, completo di un leader "" e codice paese. |
| InputParameter | throwExceptionOnFailure | boolean | [Facoltativo] Parametro che indica se viene generata un'eccezione quando il numero di telefono non è valido. Il valore predefinito è False. |
| InputParameter | countryCodeType (Tipodidominio) | string | [Facoltativo] Parametro che indica il tipo di codice paese nell'attestazione di output. I valori disponibili sono **CallingCode** (il codice di chiamata internazionale per un paese, ad esempio , n. 1) o **ISO3166** (il codice paese ISO-3166 di due lettere). |
| OutputClaim | NationalNumber | string | Attestazione della stringa per il numero nazionale del numero di telefono. |
| OutputClaim | countryCode | string | Attestazione della stringa per il codice paese del numero di telefono. |


Se la trasformazione delle attestazioni **GetNationalNumberAndCountryCodeFromPhoneNumberString** viene eseguita da un [profilo tecnico](validation-technical-profile.md) di convalida chiamato da un profilo [tecnico auto-asserito](self-asserted-technical-profile.md) o da [un'azione](display-controls.md#display-control-actions)di controllo di visualizzazione, i metadati del profilo tecnico **autoassecco UserMessageIfPhoneNumberParseFailure** controllano il messaggio di errore presentato all'utente.

![Diagramma del percorso di esecuzione dei messaggi di errore](./media/phone-authentication/assert-execution.png)

È possibile utilizzare questa trasformazione delle attestazioni per dividere un numero di telefono completo nel codice del paese e il numero nazionale. Se il numero di telefono fornito non è valido, è possibile scegliere di generare un messaggio di errore.

Nell'esempio seguente si tenta di dividere il numero di telefono in numero nazionale e codice paese. Se il numero di telefono è valido, il numero di telefono verrà sostituito dal numero nazionale. Se il numero di telefono non è valido, non verrà generata un'eccezione e il numero di telefono avrà ancora il valore originale.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Il profilo tecnico auto-asserito che chiama il profilo tecnico di convalida che contiene questa trasformazione delle attestazioni può definire il messaggio di errore.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Esempio 1

- Attestazioni di input:
  - **PhoneNumber**: 49 (123) 456-7890
- Parametri di input:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- Attestazioni di output:
  - **numeronazionale**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Esempio 2

- Attestazioni di input:
  - **PhoneNumber**: 49 (123) 456-7890
- Parametri di input
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Attestazioni di output:
  - **numeronazionale**: 1234567890
  - **CountryCode**: 49 USD
