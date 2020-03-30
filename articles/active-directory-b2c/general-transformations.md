---
title: Esempi generali di trasformazione delle attestazioni per i criteri personalizzatiGeneral claims transformation examples for custom policies
titleSuffix: Azure AD B2C
description: Esempi generali di trasformazione delle attestazioni per lo schema IEF (Identity Experience Framework) di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188546"
---
# <a name="general-claims-transformations"></a>Trasformazioni delle attestazioni generali

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso di trasformazioni di attestazioni generali dello schema Identity Experience Framework in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopiaClaim

Copiare il valore di un'attestazione a un'altra. Entrambe le attestazioni devono provenire dallo stesso tipo.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringa, int | Tipo di attestazione da copiare. |
| OutputClaim | outputClaim | stringa, int | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

Usare questa trasformazione delle attestazioni per copiare un valore da una stringa o da un'attestazione numerica a un'altra attestazione. Nell'esempio seguente viene copiato il valore dell'attestazione externalEmail per l'attestazione di posta elettronica.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**:bob@contoso.com
- Attestazioni di output:
    - **outputClaim**:bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

Controlla se **inputClaim** esiste o meno e imposta **outputClaim** su true o false di conseguenza.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Qualsiasi | L'attestazione di input di cui deve essere verificata l'esistenza. |
| OutputClaim | outputClaim | boolean | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

Usare questa trasformazione delle attestazioni per verificare se un'attestazione esiste o contiene un valore. Il valore restituito è un valore booleano che indica se l'attestazione esiste. L'esempio controlla se l'indirizzo di posta elettronica esiste.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputClaim**:someone@contoso.com
- Attestazioni di output:
  - **outputClaim**: true

## <a name="hash"></a>Hash

Eseguire l'hash del testo normale specificato usando il salt e un segreto. L'algoritmo hash utilizzato è SHA-256.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | L'attestazione di input da crittografare |
| InputClaim | salt | string | Il parametro salt. È possibile creare un valore casuale, usando le trasformazione delle attestazioni `CreateRandomString`. |
| InputParameter | randomizerSecret | string | Punta a **una chiave dei criteri**B2C di Azure AD esistente. Per creare una nuova chiave dei criteri: nel tenant B2C di Azure AD, in **Gestisci**selezionare **Identity Experience Framework**. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant. Selezionare **Aggiungi**. Selezionare **Manuale** in **Opzioni**. Specificare un nome (il prefisso *B2C_1A_* potrebbe essere aggiunto automaticamente). Nella casella di testo **Segreto** immettere qualsiasi segreto che si desidera utilizzare, ad esempio 1234567890. Per **Uso chiave** selezionare **Firma**. Selezionare **Crea**. |
| OutputClaim | hash | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. L'attestazione configurata in `plaintext` inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **testo normale**:MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Attestazioni di output:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
