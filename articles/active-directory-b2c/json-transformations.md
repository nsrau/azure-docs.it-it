---
title: Esempi di trasformazione di attestazioni JSON per criteri personalizzati
titleSuffix: Azure AD B2C
description: Esempi di trasformazione delle attestazioni JSON per lo schema Framework dell'esperienza (Identity Experience Framework) del Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 676b6abb28abf58287bfc9036ca907ae6a1ee192
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961290"
---
# <a name="json-claims-transformations"></a>Trasformazioni delle attestazioni JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni delle attestazioni JSON dello schema del Framework dell'esperienza di identità in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

Usare valori di attestazione o costanti per generare una stringa JSON. La stringa di percorso che segue la notazione del punto viene usata per indicare dove inserire i dati in una stringa JSON. Dopo la suddivisione in base ai punti, tutti i numeri interi vengono interpretati come l'indice di una matrice JSON e i valori non integer vengono interpretati come indice di un oggetto JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Qualsiasi stringa che segue la notazione del punto | Stringa | JsonPath del file JSON in cui verrà inserito il valore dell'attestazione. |
| InputParameter | Qualsiasi stringa che segue la notazione del punto | Stringa | JsonPath del codice JSON in cui verrà inserito il valore stringa costante. |
| OutputClaim | outputClaim | string | Stringa JSON generata. |

### <a name="example-1"></a>Esempio 1

Nell'esempio seguente viene generata una stringa JSON basata sul valore dell'attestazione "email" e "OTP", nonché sulle stringhe costanti.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

La trasformazione delle attestazioni seguente restituisce un'attestazione di stringa JSON che sarà il corpo della richiesta inviata a SendGrid (un provider di posta elettronica di terze parti). La struttura dell'oggetto JSON è definita dagli ID nella notazione del punto di InputParameters e TransformationClaimTypes di InputClaims. I numeri nella notazione del punto implicano matrici. I valori provengono dalle proprietà InputClaims ' Values e InputParameters ' "value".

- Attestazioni di input:
  - **indirizzo di posta elettronica**, trasformazione tipo di attestazione  **personalizzazione. 0. a. 0. messaggio di posta elettronica**: " someone@example.com "
  - **OTP**, tipo di attestazione di trasformazione **personalizations.0.dynamic_template_data. OTP** "346349"
- Parametro di input:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **da. email**: " service@contoso.com "
  - **personalizzazioni. 0. oggetto** "contoso account email verifica codice"
- Attestazione di output:
  - **requestBody**: valore JSON

```json
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

### <a name="example-2"></a>Esempio 2

Nell'esempio seguente viene generata una stringa JSON basata sui valori dell'attestazione e sulle stringhe costanti.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

La trasformazione delle attestazioni seguente restituisce un'attestazione di stringa JSON che sarà il corpo della richiesta inviata a un'API REST. La struttura dell'oggetto JSON è definita dagli ID nella notazione del punto di InputParameters e TransformationClaimTypes di InputClaims. I numeri nella notazione del punto implicano matrici. I valori provengono dalle proprietà InputClaims ' Values e InputParameters ' "value".

- Attestazioni di input:
  - **posta elettronica**, tipo di attestazione di trasformazione  **customerEntity. email**: " john.s@contoso.com "
  - **ObjectID**, tipo di attestazione di trasformazione **customerEntity. userObjectId** "01234567-89ab-cdef-0123-456789abcdef"
  - **ObjectID**, tipo di attestazione di trasformazione **customerEntity. FirstName** "John"
  - **ObjectID**, tipo di attestazione di trasformazione **customerEntity. LastName** "Smith"
- Parametro di input:
  - **customerEntity.Role.Name**: "Administrator"
  - **customerEntity.Role.ID** 1
- Attestazione di output:
  - **requestBody**: valore JSON

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Consente di ottenere un elemento specificato da un dato JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Stringa | I ClaimType che vengono usati dalla trasformazione delle attestazioni per ottenere l'elemento. |
| InputParameter | claimToExtract | Stringa | il nome dell'elemento JSON da estrarre. |
| OutputClaim | extractedClaim | Stringa | Il ClaimType che viene generato dopo aver richiamato questa trasformazione delle attestazioni, il valore dell'elemento specificato nel parametro di input _claimToExtract_. |

Nell'esempio seguente la trasformazione delle attestazioni ha estratto l'elemento `emailAddress` dai dati JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
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
| InputClaim | jsonSourceClaim | Stringa | I ClaimType usati dalla trasformazione delle attestazioni per ottenere le attestazioni. |
| InputParameter | errorOnMissingClaims | boolean | Specifica se generare un errore quando manca una delle attestazioni. |
| InputParameter | includeEmptyClaims | Stringa | Specifica se includere attestazioni vuote. |
| InputParameter | jsonSourceKeyName | Stringa | Nome della chiave dell'elemento |
| InputParameter | jsonSourceValueName | Stringa | Nome del valore dell'elemento |
| OutputClaim | Raccolta | string, int, boolean e datetime |Elenco di attestazioni da estrarre. Il nome dell'attestazione deve essere uguale a quello specificato nell'attestazione di input _jsonSourceClaim_. |

Nell'esempio seguente la trasformazione delle attestazioni estrae le attestazioni seguenti: email (string), displayName (string), membershipNum (int), active (boolean) e birthdate (datetime) dai dati JSON.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
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
  - **posta elettronica**: " someone@example.com "
  - **displayName**: "Someone"
  - **membershipNum**: 6353399
  - **active**: true
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Consente di ottenere un elemento numerico (lungo) specificato da un dato JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Stringa | I ClaimType usati dalla trasformazione delle attestazioni per ottenere l'attestazione. |
| InputParameter | claimToExtract | Stringa | Il nome dell'elemento JSON da estrarre. |
| OutputClaim | extractedClaim | long | Il ClaimType che viene generato dopo aver richiamato ClaimsTransformation, il valore dell'elemento specificato nei parametri di input _claimToExtract_. |

Nell'esempio seguente la trasformazione delle attestazioni estrae l'elemento `id` dai dati JSON.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
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

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Ottiene il primo elemento da un dato JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Stringa | Oggetto ClaimTypes usato dalla trasformazione delle attestazioni per ottenere l'elemento dai dati JSON. |
| OutputClaim | Key | string | Chiave del primo elemento in JSON. |
| OutputClaim | Valore | string | Il primo valore dell'elemento in JSON. |

Nell'esempio seguente, la trasformazione delle attestazioni estrae il primo elemento (dato il nome) dai dati JSON.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputJson**: {"givename": "Emilty", "LastName": "Smith"}
- Attestazioni di output:
  - **chiave**: given
  - **valore**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Consente di ottenere il primo elemento da una matrice di dati JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | Stringa | I ClaimType che vengono usati dalla trasformazione delle attestazioni per ottenere l'elemento dalla matrice JSON. |
| OutputClaim | extractedClaim | Stringa | Il ClaimType che viene generato dopo che è stata richiamata questa ClaimsTransformation, il primo elemento nella matrice JSON. |

Nell'esempio seguente la trasformazione delle attestazioni estrae il primo elemento (indirizzo di posta elettronica) dalla matrice JSON `["someone@example.com", "Someone", 6353399]`.

```xml
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
  - **inputJsonClaim**: [" someone@example.com ", "qualcuno", 6353399]
- Attestazioni di output:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte i dati XML nel formato JSON.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | Stringa | I ClaimType che vengono usati dalla trasformazione delle attestazioni per convertire i dati dal formato XML al formato JSON. |
| OutputClaim | json | Stringa | Il ClaimType che viene generato dopo che è stata richiamata questa ClaimsTransformation, i dati in formato JSON. |

```xml
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

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Attestazione di output:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


