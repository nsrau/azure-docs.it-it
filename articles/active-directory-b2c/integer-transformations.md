---
title: Esempi di trasformazione di attestazioni integer per criteri personalizzati
titleSuffix: Azure AD B2C
description: Esempi di trasformazione di attestazioni integer per lo schema Framework dell'esperienza (Identity Experience Framework) del Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187679"
---
# <a name="integer-claims-transformations"></a>Trasformazioni delle attestazioni integre

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni di attestazioni integer dello schema del Framework dell'esperienza di identità in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Converte un tipo di dati lungo in un tipo di dati stringa.

| Item | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | Il ClaimType da convertire in una stringa. |
| OutputClaim | outputClaim | stringa | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

In questo esempio, l'`numericUserId` attestazione con un tipo valore lungo viene convertito in un'`UserId` attestazione con un tipo valore di stringa.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**: 12334 (lungo)
- Attestazioni di output:
    - **outputClaim**: "12334" (stringa)

