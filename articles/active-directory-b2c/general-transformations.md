---
title: Esempi di trasformazione di attestazioni generali per lo schema del framework di gestione delle identità di Azure Active Directory B2C | Microsoft Docs
description: Esempi di trasformazione di attestazioni generali per lo schema del framework di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6a9a819e75e487999a2b50ae758b8d9c6c716a4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397159"
---
# <a name="general-claims-transformations"></a>Trasformazioni delle attestazioni generali

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni di attestazioni generali dello schema del framework di gestione delle identità di Azure Active Directory (Azure AD) B2C. Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

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
  - **inputClaim**: someone@contoso.com
- Attestazioni di output: 
    - **outputClaim**: true

## <a name="hash"></a>Hash

Eseguire l'hash del testo normale specificato usando il salt e un segreto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | stringa | L'attestazione di input da crittografare |
| InputClaim | salt | stringa | Il parametro salt. È possibile creare un valore casuale, usando le trasformazione delle attestazioni `CreateRandomString`. |
| InputParameter | randomizerSecret | stringa | Indirizza a **chiavi dei criteri** Azure AD B2C esistenti. Per crearne uno nuovo: Nel tenant di Azure AD B2C selezionare **Impostazioni di Azure AD B2C > Identity Experience Framework**. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant. Selezionare **Aggiungi**. Selezionare **Manuale** in **Opzioni**. Fornire un nome (il prefisso B2C_1A_ può essere aggiunto automaticamente). Nella finestra Segreto immettere qualsiasi segreto da usare, ad esempio 1234567890. In Uso chiave selezionare **Segreto**. Selezionare **Create**. |
| OutputClaim | hash | stringa | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. L'attestazione configurata in `plaintext` inputClaim. |

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
    - **plaintext**: MyPass@word1
    - **salt**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Attestazioni di output: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



