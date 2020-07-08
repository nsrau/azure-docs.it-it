---
title: Esempi di trasformazione delle attestazioni dell'account di social networking per criteri personalizzati
titleSuffix: Azure AD B2C
description: Esempi di trasformazione delle attestazioni dell'account di social networking per lo schema Framework dell'esperienza (Identity Experience Framework) del Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eaa2984c0d7a5d3763f554e39f687fdbd2865e96
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203385"
---
# <a name="social-accounts-claims-transformations"></a>Trasformazioni delle attestazioni degli account social

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C), le identità degli account di social networking vengono archiviate in un `userIdentities` attributo di un tipo di attestazione **alternativeSecurityIdCollection** . Ogni elemento di **alternativeSecurityIdCollection** specifica l'autorità di certificazione (nome del provider di identità, ad esempio facebook.com) e il `issuerUserId`, ovvero un identificatore utente univoco per l'autorità di certificazione.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

L'articolo fornisce esempi per l'uso delle trasformazioni di attestazioni dell'account social dello Schema del framework dell'esperienza di gestione delle identità in Azure AD B2C. Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Crea una rappresentazione JSON della proprietà alternativeSecurityId dell'utente che può essere usata nelle chiamate ad Azure Active Directory. Per ulteriori informazioni, vedere lo schema [AlternativeSecurityId](https://docs.microsoft.com/graph/api/resources/alternativesecurityid) .

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Key | string | Il tipo di attestazione che specifica lo UID univoco usato dal provider di identità di social networking. |
| InputClaim | identityProvider | string | Il tipo di attestazione che specifica il nome del provider di identità dell'account di social networking, ad esempio facebook.com. |
| OutputClaim | alternativeSecurityId | string | Il tipo di attestazione generato dopo che ClaimsTransformation è stato richiamato. Contiene informazioni sull'identità di un utente con account social. Il valore **autorità di certificazione** è il valore dell'attestazione `identityProvider`. Il valore **issuerUserId** è il valore dell'attestazione `key` nel formato base64. |

Usare questa trasformazione delle attestazioni per generare un tipo di attestazione `alternativeSecurityId`. Viene usato da tutti i profili tecnici di provider di identità di social networking, ad esempio `Facebook-OAUTH`. La trasformazione delle attestazioni seguente riceve l'ID dell'account di social networking dell'utente e il nome del provider di identità. L'output del profilo tecnico è un formato di stringa JSON che può essere usato nei servizi di directory di Azure AD.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **chiave**: 12334
    - **IdentityProvider**: Facebook.com
- Attestazioni di output:
    - **alternativeSecurityId**: { "autorità di certificazione": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Aggiunge un `AlternativeSecurityId` a un'attestazione `alternativeSecurityIdCollection`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Il ClaimType da aggiungere all'attestazione di output. |
| InputClaim | collection | alternativeSecurityIdCollection | I tipi di attestazione che vengono usati dalla trasformazione delle attestazioni, se disponibili nei criteri. Se specificato, la trasformazione delle attestazioni aggiunge il `item` alla fine della raccolta. |
| OutputClaim | collection | alternativeSecurityIdCollection | Gli oggetti ClaimType generati dopo che l'oggetto ClaimsTransformation è stato richiamato. La nuova raccolta che contiene sia gli elementi dell'input `collection` che `item`. |

L'esempio seguente collega una nuova identità di social networking a un account esistente. Per collegare una nuova identità di social networking:
1. Nei profili tecnici **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId**, viene fornita l'attestazione **alternativeSecurityIds** dell'utente.
1. Chiedere all'utente di accedere con uno dei provider di identità che non sono associati a tale utente.
1. Usando la trasformazione delle attestazioni **CreateAlternativeSecurityId**, creare un nuovo tipo di attestazione **alternativeSecurityId** con un nome di `AlternativeSecurityId2`
1. Chiamare la trasformazione delle attestazioni **AddItemToAlternativeSecurityIdCollection** per aggiungere l'attestazione **AlternativeSecurityId2** all'attestazione esistente **AlternativeSecurityIds**.
1. Mantenere l'attestazione **alternativeSecurityIds** per l'account utente

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **elemento**: { "autorità di certificazione": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **raccolta**: [ { "autorità di certificazione": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Attestazioni di output:
    - **raccolta**: [ { "autorità di certificazione": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Restituisce l'elenco delle autorità di certificazione dell'attestazione **alternativeSecurityIdCollection** a una nuova attestazione **stringCollection**.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Il tipo di attestazione da usare per ottenere l'elenco dei provider di identità (autorità di certificazione). |
| OutputClaim | identityProvidersCollection | stringCollection | Gli oggetti ClaimType generati dopo che l'oggetto ClaimsTransformation è stato richiamato. Elenco di provider di identità associati all'attestazione di input alternativeSecurityIdCollection |

La trasformazione delle attestazioni seguente legge l'attestazione **alternativeSecurityIds** dell'utente ed estrae l'elenco di nomi di provider di identità associati all'account. Usare l'output **identityProvidersCollection** per visualizzare l'elenco dei provider di identità associati all'account utente. Oppure, nella pagina di selezione del provider di identità, filtrare l'elenco dei provider di identità basati sull'attestazione di output **identityProvidersCollection**. Pertanto, l'utente può selezionare il collegamento a una nuova identità di social networking che non sia già associata all'account.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Attestazioni di input:
    - **alternativeSecurityIdCollection**: [ { "autorità di certificazione": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Attestazioni di output:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Rimuove un **AlternativeSecurityId** dall'attestazione **alternativeSecurityIdCollection**.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | string | Il tipo di attestazione che contiene il nome del provider di identità da rimuovere dalla raccolta. |
| InputClaim | collection | alternativeSecurityIdCollection | I tipi di attestazione che vengono usati dalla trasformazione delle attestazioni. La trasformazione delle attestazioni rimuove il provider di identità dalla raccolta. |
| OutputClaim | collection | alternativeSecurityIdCollection | Gli oggetti ClaimType generati dopo che l'oggetto ClaimsTransformation è stato richiamato. La nuova raccolta, dopo che il provider di identità è rimosso dalla raccolta. |

Nell'esempio seguente viene scollegata l'identità di social networking dall'account esistente. Per scollegare un'identità di social networking:
1. Nei profili tecnici **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId**, viene fornita l'attestazione **alternativeSecurityIds** dell'utente.
2. Chiedere all'utente di selezionare quale account di social networking rimuovere dall'elenco di provider di identità associati a tale utente.
3. Chiamare un profilo tecnico di trasformazione delle attestazioni che chiama la trasformazione delle attestazioni **RemoveAlternativeSecurityIdByIdentityProvider**, che ha rimosso l'identità di social networking selezionata, usando il nome del provider di identità.
4. Rendere permanente l'attestazione **alternativeSecurityIds** per l'account utente.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **identityProvider**: facebook.com
    - **raccolta**: [ { "autorità di certificazione": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Attestazioni di output:
    - **raccolta**: [ { "autorità di certificazione": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
