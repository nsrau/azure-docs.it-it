---
title: Esempi di trasformazione di attestazioni di stringa per lo schema del framework di gestione delle identità di Azure Active Directory B2C | Microsoft Docs
description: Esempi di trasformazione di attestazioni di stringa per lo schema del framework di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a06447aaa6579052285e7e2cd93bf40183ed173f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512597"
---
# <a name="string-claims-transformations"></a>Trasformazioni di attestazioni di stringa

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo contiene esempi per l'uso delle trasformazioni di attestazioni di stringa dello schema del framework di gestione delle identità di Azure Active Directory (Azure AD) B2C. Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

Confronta due attestazioni e genera un'eccezione se non sono uguali in base agli elementi inputClaim1, inputClaim2 e stringComparison del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
| inputClaim | inputClaim2 | string | Tipo della seconda attestazione di cui eseguire il confronto. |
| InputParameter | stringComparison | string | Confronto tra le stringhe con valore Ordinal o OrdinalIgnoreCase. |

La trasformazione dell'asserzione **AssertStringClaimsAreEqual** viene sempre eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) chiamato da un [profilo tecnico autocertificato](self-asserted-technical-profile.md). I metadati del profilo tecnico autocertificato **UserMessageIfClaimsTransformationStringsAreNotEqual** controllano il messaggio di errore inviato all'utente.

![Esecuzione di AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

È possibile usare questa trasformazione per verificare che due elementi ClaimType abbiano lo stesso valore. In caso contrario, viene generato un messaggio di errore. L'esempio seguente controlla che l'elemento ClaimType **strongAuthenticationEmailAddress** sia uguale all'elemento ClaimType **email**. In caso contrario, viene generato un messaggio di errore. 

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Il profilo tecnico di convalida **login-NonInteractive** chiama la trasformazione di attestazioni **AssertEmailAndStrongAuthenticationEmailAddressAreEqual**.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Il profilo tecnico autocertificato chiama il profilo tecnico **login-NonInteractive** di convalida.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
    - Parametri di input:
  - **stringComparison**:  ordinalIgnoreCase
- Risultato: errore generato

## <a name="changecase"></a>ChangeCase 

Modifica le maiuscole/minuscole dell'attestazione specificata a seconda dell'operatore.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Elemento ClaimType modificato. |
| InputParameter | toCase | string | Uno dei valori seguenti: `LOWER` o `UPPER`. |
| OutputClaim | outputClaim | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

Usare questa trasformazione per cambiare le maiuscole/minuscole di qualsiasi stringa ClaimType.  

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **email**: SomeOne@contoso.com
- Parametri di input:
    - **toCase**: LOWER
- Attestazioni di output:
  - **email**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim 

Crea un'attestazione di stringa dal parametro di input specificato nei criteri.

| Elemento | TransformationClaimType | Tipo di dati | Note |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | Stringa da impostare |
| OutputClaim | createdClaim | string | Tipo attestazione generato dopo che questa trasformazione di attestazioni è stato richiamato con il valore specificato nel parametro di input. |

Usare questa trasformazione di attestazioni per impostare un valore ClaimType di stringa.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Parametro di input:
    - **value**: Condizioni d'uso di Contoso...
- Attestazioni di output:
    - **createdClaim**: l'elemento TOS ClaimType contiene il valore "Condizioni d'uso di Contoso...".

## <a name="compareclaims"></a>CompareClaims

Determina se un'attestazione di stringa è uguale a un'altra. Il risultato è un nuovo elemento ClaimType booleano con il valore `true` o `false`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
| inputClaim | inputClaim2 | string | Tipo della seconda attestazione di cui eseguire il confronto. |
| InputParameter | operator | string | I valori possibili sono: `EQUAL` o `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Specifica se il confronto deve ignorare l'uso di maiuscole e minuscole nelle stringhe da confrontare. |
| OutputClaim | outputClaim | boolean | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

Usare questa trasformazione di attestazioni per verificare se un'attestazione è uguale a un'altra. La trasformazione di attestazioni seguente, ad esempio, controlla se il valore dell'attestazione **email** è uguale a quello dell'attestazione **Verified.Email**.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parametri di input:
    - **operator**:  NOT EQUAL
    - **ignoreCase**: true
- Attestazioni di output:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se un valore di attestazione è uguale al valore del parametro di input.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
| InputParameter | operator | string | I valori possibili sono: `EQUAL` o `NOT EQUAL`. |
| InputParameter | compareTo | string | Confronto tra le stringhe con valore Ordinal o OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Specifica se il confronto deve ignorare l'uso di maiuscole e minuscole nelle stringhe da confrontare. |
| OutputClaim | outputClaim | boolean | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

È possibile usare questa trasformazione di attestazioni per controllare se un'attestazione è uguale a un valore specificato. La trasformazione di attestazioni seguente, ad esempio, controlla se il valore dell'attestazione **termsOfUseConsentVersion** è uguale a `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio
- Attestazioni di input:
    - **inputClaim1**: v1
- Parametri di input:
    - **compareTo**: V1
    - **operator**: EQUAL 
    - **ignoreCase**:  true
- Attestazioni di output:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Crea una stringa casuale tramite il generatore di numeri casuali. Se il generatore di numeri casuali è di tipo `integer`, possono essere specificati un valore di inizializzazione e un numero massimo. Un parametro di formato di stringa facoltativo consente la formattazione dell'output e un parametro base64 facoltativo specifica se l'output è un elemento randomGeneratorType [guid, integer] o un elemento outputClaim (String) codificato in formato base64.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Specifica il valore casuale da generare `GUID` (ID univoco globale) o `INTEGER` (numero). |
| InputParameter | stringFormat | string | [Facoltativo] Formatta il valore casuale. |
| InputParameter | base64 | boolean | [Facoltativo] Converte il valore casuale in base 64. Se si applica il formato della stringa, il valore successivo è codificato in formato base64. |
| InputParameter | maximumNumber | int | [Facoltativo] Solo per elementi randomGeneratorType di tipo `INTEGER`. Specificare il numero massimo. |
| InputParameter | seed  | int | [Facoltativo] Solo per elementi randomGeneratorType di tipo `INTEGER`. Specifica il valore di inizializzazione per il valore casuale. Nota: uno stesso valore di inizializzazione genera la stessa sequenza di numeri casuali. |
| OutputClaim | outputClaim | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. Valore casuale. |

L'esempio seguente genera un ID univoco globale. Questa trasformazione di attestazioni viene usata per creare un nome UPN casuale (nome dell'entità utente).

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Esempio

- Parametri di input:
    - **randomGeneratorType**: GUID
- Attestazioni di output: 
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

L'esempio seguente genera un valore intero casuale compreso tra 0 e 1000. Il valore viene formattato su OTP_{valore casuale}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Parametri di input:
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: false
- Attestazioni di output: 
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formatta un'attestazione in base alla stringa formato specificata. Questa trasformazione usa il metodo C# `String.Format`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string |Elemento ClaimType che funge come parametro {0} del formato della stringa. |
| InputParameter | stringFormat | string | Formato della stringa, ad esempio il parametro {0}. |
| OutputClaim | outputClaim | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

Usare questa trasformazione di attestazioni per formattare qualsiasi stringa con un parametro {0}. L'esempio seguente crea un elemento **userPrincipalName**. Tutti i profili tecnici di provider di identità social, ad esempio `Facebook-OAUTH`, chiamano **CreateUserPrincipalName** per generare un elemento **userPrincipalName**.   

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parametri di input:
    - **stringFormat**:  cpim_{0}@{RelyingPartyTenantId}
- Attestazioni di output:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formatta due attestazioni in base alla stringa di formato specificata. Questa trasformazione usa il metodo C# **String.Format**.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | Elemento ClaimType che funge come parametro {0} del formato della stringa. |
| InputClaim | inputClaim | string | Elemento ClaimType che funge come parametro {1} del formato della stringa. |
| InputParameter | stringFormat | string | Formato della stringa, ad esempio i parametri {0} e {1}. |
| OutputClaim | outputClaim | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

Usare questa trasformazione di attestazioni per formattare qualsiasi stringa con due parametri, {0} e {1}. L'esempio seguente crea un elemento **displayName** con il formato specificato:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parametri di input:
    - **stringFormat**: {0} {1}
- Attestazioni di output:
    - **outputClaim**: Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Cerca un elemento da un'attestazione da una raccolta **Restriction** di attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | Attestazione che contiene il testo in cui eseguire la ricerca nelle attestazioni **restrictionValueClaim** con la raccolta **Restriction**.  |
| OutputClaim | restrictionValueClaim | string | Attestazione che contiene la raccolta **Restriction**. Dopo che la trasformazione di attestazioni è stata richiamata, il valore di questa attestazione contiene il valore dell'elemento selezionato. |

L'esempio seguente cerca la descrizione del messaggio di errore in base alla chiave di errore. L'attestazione **responseMsg** contiene una raccolta di messaggi di errori da presentare all'utente finale o da inviare alla relying party.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
La trasformazione di attestazioni esegue la ricerca del testo dell'elemento e ne restituisce il valore. Se la restrizione è localizzata usando `<LocalizedCollection>`, la trasformazione di attestazioni restituisce il valore localizzato.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />         
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **mapFromClaim**: B2C_V1_90001
- Attestazioni di output:
    - **restrictionValueClaim**: Accesso non consentito ai minorenni.

## <a name="lookupvalue"></a>LookupValue

Esegue la ricerca di un valore di attestazione da un elenco di valori in base al valore di un'altra attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | Attestazione che contiene il valore di ricerca |
| InputParameter | |string | Raccolta di elementi inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Controlla se viene restituito un errore quando non esiste alcuna ricerca corrispondente. |
| OutputClaim | inputParameterId | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. Valore dell'ID corrispondente. |

L'esempio seguente cerca il nome di dominio in una delle raccolte inpuParameters. La trasformazione delle attestazioni esegue la ricerca del nome di dominio nell'identificatore e ne restituisce il valore (ID applicazione).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputParameterId**: test.com
- Parametri di input:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Attestazioni di output:
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Pulisce il valore di una determinata attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | Attestazione il cui valore deve essere NULL. |

Usare questa trasformazione di attestazioni per rimuovere i dati non necessari dall'elenco di proprietà delle attestazioni. Il cookie di sessione, di conseguenza, sarà di dimensioni minori. L'esempio seguente rimuove il valore del tipo di attestazione `TermsOfService`.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Attestazioni di input:
    - **outputClaim**: App Contoso. Se continui a esplorare e a usare questo sito Web, accetti di rispettare i termini e le condizioni seguenti...
- Attestazioni di output:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Ottiene la parte di dominio di un indirizzo di posta elettronica.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | Elemento ClaimType che contiene l'indirizzo di posta elettronica. |
| OutputClaim | dominio | string | Elemento ClaimType (dominio) generato dopo che è stata richiamata questa trasformazione di attestazioni. |

Usare questa trasformazione di attestazioni per analizzare il nome di dominio dopo il simbolo @ dell'utente. Può essere utile per la rimozione delle informazioni personali dai dati di controllo. La trasformazione di attestazioni seguenti dimostra come analizzare il nome di dominio da un'attestazione **email**.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **emailAddress**: joe@outlook.com
- Attestazioni di output:
    - **domain**: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica che un'attestazione di stringa e il parametro di input `matchTo` siano uguali e imposta le attestazioni di output con il valore presente nei parametri di input `stringMatchMsg` e `stringMatchMsgCode`, insieme all'attestazione di output del risultato di confronto che deve essere impostato come `true` o `false` in base al risultato del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Tipo dell'attestazione di cui eseguire il confronto. |
| InputParameter | matchTo | string | Stringa da confrontare con `inputClaim`. |
| InputParameter | stringComparison | string | I valori possibili sono: `Ordinal` o `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | Primo valore da impostare se le stringhe sono uguali. |
| InputParameter | stringMatchMsgCode | string | Secondo valore da impostare se le stringhe sono uguali. |
| OutputClaim | outputClaim1 | string | Se le stringhe sono uguali, l'attestazione di output contiene il valore del parametro di output `stringMatchMsg`. |
| OutputClaim | outputClaim2 | string | Se le stringhe sono uguali, l'attestazione di output contiene il valore del parametro di output `stringMatchMsgCode`. |
| OutputClaim | stringCompareResultClaim | boolean | Tipo dell'attestazione di output del risultato del confronto che deve essere impostato come `true` o `false` in base al risultato del confronto. |

È possibile usare questa trasformazione di attestazioni per controllare se un'attestazione è uguale a un valore specificato. La trasformazione di attestazioni seguente, ad esempio, controlla se il valore dell'attestazione **termsOfUseConsentVersion** è uguale a `v1`. In caso affermativo, impostare il valore su `v2`. 

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**: v1
- Parametri di input:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase 
    - **stringMatchMsg**:  B2C_V1_90005
    - **stringMatchMsgCode**:  Il tipo di servizio è aggiornato alla versione 2
- Attestazioni di output:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: Il tipo di servizio è aggiornato alla versione 2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica che un'attestazione di stringa e il parametro di input `matchTo` siano uguali e imposta le attestazioni di output con il valore presente nel parametro di input `outputClaimIfMatched`, insieme all'attestazione di output del risultato di confronto che deve essere impostato come `true` or `false` in base al risultato del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | Tipo dell'attestazione di cui eseguire il confronto. |
| InputParameter | matchTo | string | Stringa da confrontare con inputClaim. |
| InputParameter | stringComparison | string | I valori possibili sono: `Ordinal` o `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | Valore da impostare se le stringhe sono uguali. |
| OutputClaim | outputClaim | string | Se le stringhe sono uguali, l'attestazione di output contiene il valore del parametro di output `outputClaimIfMatched`. Se le stringhe non corrispondono, il valore contenuto è null. |
| OutputClaim | stringCompareResultClaim | boolean | Tipo dell'attestazione di output del risultato del confronto che deve essere impostato come `true` o `false` in base al risultato del confronto. |

La trasformazione di attestazioni seguente, ad esempio, controlla se il valore dell'attestazione **ageGroup** è uguale a `Minor`. In caso affermativo, restituisce il valore a `B2C_V1_90001`. 

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **claimToMatch**: Minorenne
- Parametri di input:
    - **matchTo**: Minorenne
    - **stringComparison**: ordinalIgnoreCase 
    - **outputClaimIfMatched**:  B2C_V1_90001
- Attestazioni di output:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true

