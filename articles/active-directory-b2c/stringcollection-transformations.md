---
title: Esempi di trasformazione delle attestazioni StringCollection per i criteri personalizzati
titleSuffix: Azure AD B2C
description: Esempi di trasformazione delle attestazioni StringCollection per lo schema Framework dell'esperienza (Identity Experience Framework) del Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1ad9cc3d6d07c8d744ec667e2fffb035848121b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203249"
---
# <a name="stringcollection-claims-transformations"></a>Trasformazioni delle attestazioni StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni delle attestazioni della raccolta di stringhe dello schema del Framework dell'esperienza di identità in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Aggiunge un'attestazione stringa a una nuova attestazione StringCollection di valori univoci.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Il ClaimType da aggiungere all'attestazione di output. |
| InputClaim | collection | stringCollection | [Facoltativo] Se specificato, la trasformazione delle attestazioni copia gli elementi da questa raccolta e aggiunge l'elemento alla fine dell'attestazione di raccolta di output. |
| OutputClaim | collection | stringCollection | ClaimType generato dopo che è stata richiamata la trasformazione delle attestazioni, con il valore specificato nell'attestazione di input. |

Usare questa trasformazione delle attestazioni per aggiungere una stringa a un oggetto stringCollection nuovo o esistente. Viene comunemente usato in un profilo tecnico **AAD-UserWriteUsingAlternativeSecurityId**. Prima che venga creato un nuovo account di social networking, la trasformazione di attestazioni **CreateOtherMailsFromEmail** legge il ClaimType e aggiunge il valore al ClaimType **otherMails**.

La trasformazione delle attestazioni seguente aggiunge il ClaimType del **messaggio di posta elettronica** al ClaimType **otherMails**.

```xml
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
  - **raccolta**: [" someone@outlook.com "]
  - **elemento**: " admin@contoso.com "
- Attestazioni di output:
  - **raccolta**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Aggiunge un parametro di stringa a una nuova attestazione StringCollection di valori univoci.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [Facoltativo] Se specificato, la trasformazione delle attestazioni copia gli elementi da questa raccolta e aggiunge l'elemento alla fine dell'attestazione di raccolta di output. |
| InputParameter | item | string | Il valore da aggiungere all'attestazione di output. |
| OutputClaim | collection | stringCollection | Tipo attestazione generato dopo che questa trasformazione di attestazioni è stato richiamato con il valore specificato nel parametro di input. |

Usare questa trasformazione delle attestazioni per aggiungere un valore di stringa a un oggetto stringCollection nuovo o esistente. L'esempio seguente aggiunge un indirizzo di posta elettronica costante (admin@contoso.com) all'attestazione **otherMails**.

```xml
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
  - **raccolta**: [" someone@outlook.com "]
- Parametri di input
  - **elemento**: " admin@contoso.com "
- Attestazioni di output:
  - **raccolta**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Consente di ottenere il primo elemento della raccolta di stringhe fornita.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | I ClaimType che vengono usati dalla trasformazione delle attestazioni per ottenere l'elemento. |
| OutputClaim | extractedItem | string | Gli oggetti ClaimType generati dopo che l'oggetto ClaimsTransformation è stato richiamato. Primo elemento nella raccolta. |

L'esempio seguente legge l'attestazione **otherMails** e restituisce il primo elemento nell'attestazione **email**.

```xml
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
  - **raccolta**: [" someone@outlook.com ", " someone@contoso.com "]
- Attestazioni di output:
  - **extractedItem**: " someone@outlook.com "


## <a name="stringcollectioncontains"></a>StringCollectionContains

Verifica se un tipo di attestazione StringCollection contiene un elemento

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Tipo di attestazione in cui eseguire la ricerca. |
|InputParameter|item|string|Valore da cercare.|
|InputParameter|ignoreCase|string|Specifica se il confronto deve ignorare l'uso di maiuscole e minuscole nelle stringhe da confrontare.|
| OutputClaim | outputClaim | boolean | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. Indicatore booleano se la raccolta contiene una stringa di questo tipo |

Nell'esempio seguente viene controllato se il `roles` tipo di attestazione StringCollection contiene il valore **admin**.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Attestazioni di input:
    - **attestazione**: ["Reader", "Author", "admin"]
- Parametri di input:
    - **elemento**: "admin"
    - **IgnoreCase**: "true"
- Attestazioni di output:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Verifica se un tipo di attestazione StringCollection contiene un valore di attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Tipo di attestazione in cui eseguire la ricerca. |
| InputClaim | item|string| Tipo di attestazione che contiene il valore da cercare.|
|InputParameter|ignoreCase|string|Specifica se il confronto deve ignorare l'uso di maiuscole e minuscole nelle stringhe da confrontare.|
| OutputClaim | outputClaim | boolean | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. Indicatore booleano se la raccolta contiene una stringa di questo tipo |

Nell'esempio seguente viene verificato se il `roles` tipo di attestazione StringCollection contiene il valore del `role` tipo di attestazione.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Attestazioni di input:
    - **raccolta**: ["Reader", "Author", "admin"]
    - **elemento**: "admin"
- Parametri di input:
    - **IgnoreCase**: "true"
- Attestazioni di output:
    - **outputClaim**: "true"
