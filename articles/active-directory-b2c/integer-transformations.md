---
title: Esempi di trasformazione di attestazioni integre per lo schema del framework di gestione delle identità di Azure Active Directory B2C | Microsoft Docs
description: Esempi di trasformazione di attestazioni integre per lo schema del framework di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 358ee07b8fd32edded084d406e490cae9f557fdd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159508"
---
# <a name="integer-claims-transformations"></a>Trasformazioni delle attestazioni integre

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni di attestazioni integre dello schema del framework di gestione delle identità di Azure Active Directory (Azure AD) B2C. Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Converte un tipo di dati lungo in un tipo di dati stringa.

| Elemento | TransformationClaimType | Tipo di dati | Note |
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
    - **inputClaim**: "12334" (valore lungo)
- Attestazioni di output: 
    - **outputClaim**: "12334" (valore di stringa)

