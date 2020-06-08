---
title: Trasformazioni delle attestazioni di numeri di telefono nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni di riferimento sui criteri personalizzati per le trasformazioni delle attestazioni di numeri di telefono in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8052f94755019d8ad3fe818d979d2eb7f8ba0a5e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738762"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definire le trasformazioni delle attestazioni di numeri di telefono in Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo offre informazioni di riferimento ed esempi per l'uso delle trasformazioni delle attestazioni di numeri di telefono dello schema Identity Experience Framework in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni sulle trasformazioni delle attestazioni in generale, vedere [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Converte un tipo di dati `phoneNumber` in un tipo di dati `string`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  Il ClaimType da convertire in una stringa. |
| OutputClaim | phoneNumberString | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

In questo esempio, l'attestazione cellPhoneNumber con un valore di tipo `phoneNumber` viene convertita in un'attestazione cellPhone con un valore di tipo `string`.

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
  - **phoneNumber**: +11234567890 (phoneNumber)
- Attestazioni di output:
  - **phoneNumberString**: +11234567890 (string)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Questa trasformazione dell'attestazione convalida il formato del numero di telefono. Se il formato è valido, modificarlo in un formato standard usato da Azure AD B2C. Se il numero di telefono specificato non è in un formato valido, viene restituito un messaggio di errore.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | string |  Attestazione stringa per il numero di telefono. Il numero di telefono deve essere in formato internazionale, preceduto da un simbolo "+" e un codice paese/area geografica. Se viene specificata l'attestazione di input `country`, il numero di telefono è in formato locale (senza il codice paese/area geografica). |
| InputClaim | country | string | [Facoltativo] Attestazione stringa per il codice paese/area geografica del numero di telefono in formato ISO3166 (codice paese/area geografica ISO-3166 composto da due lettere). |
| OutputClaim | outputClaim | phoneNumber | Risultato della trasformazione delle attestazioni. |

La trasformazione delle attestazioni **ConvertStringToPhoneNumberClaim** viene sempre eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) chiamato da un [profilo tecnico autocertificato](self-asserted-technical-profile.md) o un [controllo di visualizzazione](display-controls.md). I metadati del profilo tecnico autocertificato **UserMessageIfClaimsTransformationInvalidPhoneNumber** controllano il messaggio di errore presentato all'utente.

![Diagramma del percorso di esecuzione del messaggio di errore](./media/phone-authentication/assert-execution.png)

È possibile usare questa trasformazione delle attestazioni per verificare che l'attestazione stringa fornita sia un numero di telefono valido. In caso contrario, viene generato un messaggio di errore. L'esempio seguente verifica che ClaimType **phoneString** sia veramente un numero di telefono valido e quindi restituisce il numero di telefono nel formato standard di Azure AD B2C. In caso contrario, viene generato un messaggio di errore.

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

Il profilo tecnico autocertificato che chiama il profilo tecnico di convalida contenente questa trasformazione delle attestazioni può definire il messaggio di errore.

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
  - **country**: DK
- Attestazioni di output:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>Esempio 2

- Attestazioni di input:
  - **phoneNumberString**: +1 (123) 456-7890
- Attestazioni di output:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Questa trasformazione estrae il codice paese/area geografica e il numero locale dall'attestazione di input e, facoltativamente, genera un'eccezione se il numero di telefono specificato non è valido.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | string | Attestazione stringa del numero di telefono. Il numero di telefono deve essere in formato internazionale, preceduto da un simbolo "+" e un codice paese/area geografica. |
| InputParameter | throwExceptionOnFailure | boolean | [Facoltativo] Parametro che indica se viene generata un'eccezione quando il numero di telefono non è valido. Il valore predefinito è False. |
| InputParameter | countryCodeType | string | [Facoltativo] Parametro che indica il tipo di codice paese/area geografica presente nell'attestazione di output. I valori disponibili sono **CallingCode** (codice di chiamata internazionale di un paese o area geografica, ad esempio +1) oppure **ISO3166** (codice paese/area geografica ISO-3166 composto da due lettere). |
| OutputClaim | nationalNumber | string | Attestazione stringa per il numero locale del numero di telefono. |
| OutputClaim | countryCode | string | Attestazione stringa per il codice paese/area geografica del numero di telefono. |


Se la trasformazione delle attestazioni **GetNationalNumberAndCountryCodeFromPhoneNumberString** viene eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) chiamato da un [profilo tecnico autocertificato](self-asserted-technical-profile.md) o un'[azione di controllo visualizzata](display-controls.md#display-control-actions), i metadati del profilo tecnico autocertificato **UserMessageIfPhoneNumberParseFailure** controllano il messaggio di errore presentato all'utente.

![Diagramma del percorso di esecuzione del messaggio di errore](./media/phone-authentication/assert-execution.png)

È possibile usare questa trasformazione delle attestazioni per dividere un numero di telefono completo in codice paese/area geografica e numero locale. Se il numero di telefono specificato non è valido, è possibile scegliere di generare un messaggio di errore.

L'esempio seguente prova a dividere il numero di telefono in numero locale e codice paese/area geografica. Se il numero di telefono è valido, tale numero verrà sostituito dal numero locale. Se il numero di telefono non è valido, verrà generata un'eccezione e il numero manterrà il valore originario.

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

Il profilo tecnico autocertificato che chiama il profilo tecnico di convalida contenente questa trasformazione delle attestazioni può definire il messaggio di errore.

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
  - **phoneNumber**: +49 (123) 456-7890
- Parametri di input:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- Attestazioni di output:
  - **nationalNumber**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Esempio 2

- Attestazioni di input:
  - **phoneNumber**: +49 (123) 456-7890
- Parametri di input
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Attestazioni di output:
  - **nationalNumber**: 1234567890
  - **countryCode**: +49
