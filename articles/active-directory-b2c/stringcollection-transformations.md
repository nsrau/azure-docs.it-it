---
title: Esempi di trasformazione delle attestazioni StringCollection per i criteri personalizzati
titleSuffix: Azure AD B2C
description: Esempi di trasformazione delle attestazioni StringCollection per lo schema Framework dell'esperienza (Identity Experience Framework) del Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fbbd7b4bdddf2b58e66cb1203414b5a63eec2f27
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951004"
---
# <a name="stringcollection-claims-transformations"></a>Trasformazioni delle attestazioni StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni delle attestazioni della raccolta di stringhe dello schema del Framework dell'esperienza di identità in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Consente di aggiungere un'attestazione di stringa a una nuova attestazione stringCollection.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Il ClaimType da aggiungere all'attestazione di output. |
| InputClaim | raccolta | stringCollection | [Facoltativo] Se specificato, la trasformazione delle attestazioni copia gli elementi da questa raccolta e aggiunge l'elemento alla fine dell'attestazione di raccolta di output. |
| OutputClaim | raccolta | stringCollection | I tipi di attestazione generati dopo che ClaimsTransformation è stato richiamato. |

Usare questa trasformazione delle attestazioni per aggiungere una stringa a un oggetto stringCollection nuovo o esistente. Viene comunemente usato in un profilo tecnico **AAD-UserWriteUsingAlternativeSecurityId**. Prima che venga creato un nuovo account di social networking, la trasformazione di attestazioni **CreateOtherMailsFromEmail** legge il ClaimType e aggiunge il valore al ClaimType **otherMails**.

La trasformazione delle attestazioni seguente aggiunge il ClaimType del **messaggio di posta elettronica** al ClaimType **otherMails**.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **collection**: ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- Attestazioni di output:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Consente di aggiungere un parametro di stringa a una nuova attestazione stringCollection.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | raccolta | stringCollection | [Facoltativo] Se specificato, la trasformazione delle attestazioni copia gli elementi da questa raccolta e aggiunge l'elemento alla fine dell'attestazione di raccolta di output. |
| InputParameter | item | string | Il valore da aggiungere all'attestazione di output. |
| OutputClaim | raccolta | stringCollection | I ClaimType generati dopo che ClaimsTransformation è stato richiamato. |

Usare questa trasformazione delle attestazioni per aggiungere un valore di stringa a un oggetto stringCollection nuovo o esistente. L'esempio seguente aggiunge un indirizzo di posta elettronica costante (admin@contoso.com) all'attestazione **otherMails**.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **collection**: ["someone@outlook.com"]
- Parametri di input
  - **item**: "admin@contoso.com"
- Attestazioni di output:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Consente di ottenere il primo elemento della raccolta di stringhe fornita.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | raccolta | stringCollection | I ClaimType che vengono usati dalla trasformazione delle attestazioni per ottenere l'elemento. |
| OutputClaim | extractedItem | string | I tipi di attestazione generati dopo che ClaimsTransformation è stato richiamato. Primo elemento nella raccolta. |

L'esempio seguente legge l'attestazione **otherMails** e restituisce il primo elemento nell'attestazione **email**.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **collection**: ["someone@outlook.com", "someone@contoso.com"]
- Attestazioni di output:
  - **extractedItem**: "someone@outlook.com"

