---
title: Esempi di trasformazione delle attestazioni JSON per lo schema del framework di gestione delle identità di Azure Active Directory B2C | Microsoft Docs
description: Esempi di trasformazione delle attestazioni JSON per lo schema del framework di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7574327f8acbd2215080e43a57b0b9c7cdd8b423
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710246"
---
# <a name="json-claims-transformations"></a>Trasformazioni delle attestazioni JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni di attestazioni JSON dello schema del framework di gestione delle identità di Azure Active Directory (Azure AD) B2C. Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Consente di ottenere un elemento specificato da un dato JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | I ClaimType che vengono usati dalla trasformazione delle attestazioni per ottenere l'elemento. |
| InputParameter | claimToExtract | string | il nome dell'elemento JSON da estrarre. |
| OutputClaim | extractedClaim | string | Il ClaimType che viene generato dopo aver richiamato questa trasformazione delle attestazioni, il valore dell'elemento specificato nel parametro di input _claimToExtract_. |

Nell'esempio seguente la trasformazione delle attestazioni ha estratto l'elemento `emailAddress` dai dati JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parametro di input:
    - **claimToExtract**: emailAddress
- Attestazioni di output: 
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Consente di ottenere un elenco di elementi specificati dai dati Json.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | I ClaimType usati dalla trasformazione delle attestazioni per ottenere le attestazioni. |
| InputParameter | errorOnMissingClaims | boolean | Specifica se generare un errore quando manca una delle attestazioni. |
| InputParameter | includeEmptyClaims | string | Specifica se includere attestazioni vuote. |
| InputParameter | jsonSourceKeyName | string | Nome della chiave dell'elemento |
| InputParameter | jsonSourceValueName | string | Nome del valore dell'elemento |
| OutputClaim | Raccolta | string, int, boolean e datetime |Elenco di attestazioni da estrarre. Il nome dell'attestazione deve essere uguale a quello specificato nell'attestazione di input _jsonSourceClaim_. |

Nell'esempio seguente la trasformazione delle attestazioni estrae le attestazioni seguenti: email (string), displayName (string), membershipNum (int), active (boolean) e birthdate (datetime) dai dati JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Attestazioni di input:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Parametri di input:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Attestazioni di output:
  - **email**: "someone@example.com"
  - **displayName**: "Someone"
  - **membershipNum**: 6353399
  - **active**: true
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Consente di ottenere un elemento numerico (lungo) specificato da un dato JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | I ClaimType usati dalla trasformazione delle attestazioni per ottenere l'attestazione. |
| InputParameter | claimToExtract | string | Il nome dell'elemento JSON da estrarre. |
| OutputClaim | extractedClaim | long | Il ClaimType che viene generato dopo aver richiamato ClaimsTransformation, il valore dell'elemento specificato nei parametri di input _claimToExtract_. |

Nell'esempio seguente la trasformazione delle attestazioni estrae l'elemento `id` dai dati JSON.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Parametri di input
    - **claimToExtract**:  id
- Attestazioni di output: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Consente di ottenere il primo elemento da una matrice di dati JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | I ClaimType che vengono usati dalla trasformazione delle attestazioni per ottenere l'elemento dalla matrice JSON. |
| OutputClaim | extractedClaim | string | Il ClaimType che viene generato dopo che è stata richiamata questa ClaimsTransformation, il primo elemento nella matrice JSON. |

Nell'esempio seguente la trasformazione delle attestazioni estrae il primo elemento (indirizzo di posta elettronica) dalla matrice JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Attestazioni di output: 
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte i dati XML nel formato JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | string | I ClaimType che vengono usati dalla trasformazione delle attestazioni per convertire i dati dal formato XML al formato JSON. |
| OutputClaim | json | string | Il ClaimType che viene generato dopo che è stata richiamata questa ClaimsTransformation, i dati in formato JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

Nell'esempio seguente la trasformazione delle attestazioni converte i dati XML seguenti nel formato JSON.

#### <a name="example"></a>Esempio
Attestazione di input:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Attestazione di output:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

