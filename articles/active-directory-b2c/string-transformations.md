---
title: Esempi di trasformazione delle attestazioni stringa per i criteri personalizzatiString claims transformation examples for custom policies
titleSuffix: Azure AD B2C
description: Esempi di trasformazione delle attestazioni stringa per lo schema IEF (Identity Experience Framework) di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f08107874598a68fb5ce2a1a8a98b6a81d7b94d4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756785"
---
# <a name="string-claims-transformations"></a>Trasformazioni di attestazioni di stringa

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni delle attestazioni di stringa dello schema Identity Experience Framework in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Confronta due attestazioni e genera un'eccezione se non sono uguali in base agli elementi inputClaim1, inputClaim2 e stringComparison del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
| InputClaim | inputClaim2 | string | Tipo della seconda attestazione di cui eseguire il confronto. |
| InputParameter | stringComparison | string | Confronto tra le stringhe con valori Ordinal e OrdinalIgnoreCase. |

La trasformazione **delle attestazioni AssertStringClaimsAreEqual** viene sempre eseguita da un [profilo tecnico](validation-technical-profile.md) di convalida chiamato da un profilo [tecnico auto-asserito](self-asserted-technical-profile.md)o da un [oggetto DisplayConrtol](display-controls.md). I `UserMessageIfClaimsTransformationStringsAreNotEqual` metadati di un profilo tecnico auto-asserito controllano il messaggio di errore che viene presentato all'utente. I messaggi di errore possono essere [localizzati.](localization-string-ids.md#claims-transformations-error-messages)


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
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parametri di input:
  - **stringComparison**:  ordinalIgnoreCase
- Risultato: errore generato

## <a name="changecase"></a>ChangeCase

Modifica le maiuscole/minuscole dell'attestazione specificata a seconda dell'operatore.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Oggetto ClaimType da modificare. |
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
  - **e-mail**:SomeOne@contoso.com
- Parametri di input:
    - **toCase**: LOWER
- Attestazioni di output:
  - **e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Crea un'attestazione stringa dal parametro di input fornito nella trasformazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
|----- | ----------------------- | --------- | ----- |
| InputParameter | Valore | string | Stringa da impostare. Questo parametro di input supporta le espressioni di [trasformazione delle attestazioni stringa.](string-transformations.md#string-claim-transformations-expressions) |
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
| InputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
| InputClaim | inputClaim2 | string | Tipo della seconda attestazione di cui eseguire il confronto. |
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
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parametri di input:
    - **operator**:  NOT EQUAL
    - **ignoreCase**: true
- Attestazioni di output:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se un valore di attestazione è uguale al valore del parametro di input.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
| InputParameter | operator | string | I valori possibili sono: `EQUAL` o `NOT EQUAL`. |
| InputParameter | compareTo | string | Confronto tra le stringhe con valori Ordinal e OrdinalIgnoreCase. |
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
| InputParameter | maximumNumber | INT | [Facoltativo] Solo per elementi randomGeneratorType di tipo `INTEGER`. Specificare il numero massimo. |
| InputParameter | seed  | INT | [Facoltativo] Solo per elementi randomGeneratorType di tipo `INTEGER`. Specifica il valore di inizializzazione per il valore casuale. Nota: uno stesso valore di inizializzazione genera la stessa sequenza di numeri casuali. |
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
| InputParameter | stringFormat | string | Formato della stringa, ad esempio il parametro {0}. Questo parametro di input supporta le espressioni di [trasformazione delle attestazioni stringa.](string-transformations.md#string-claim-transformations-expressions)  |
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
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formatta due attestazioni in base alla stringa di formato specificata. Questa trasformazione usa il metodo C# `String.Format`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | Elemento ClaimType che funge come parametro {0} del formato della stringa. |
| InputClaim | inputClaim | string | Elemento ClaimType che funge come parametro {1} del formato della stringa. |
| InputParameter | stringFormat | string | Formato della stringa, ad esempio i parametri {0} e {1}. Questo parametro di input supporta le espressioni di [trasformazione delle attestazioni stringa.](string-transformations.md#string-claim-transformations-expressions)   |
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
    - **stringFormat** {0} :{1}
- Attestazioni di output:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Copia le stringhe localizzate nelle attestazioni.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | Nome della stringa localizzata | string | Elenco di tipi di attestazione generati dopo che è stata richiamata questa trasformazione delle attestazioni. |

Per usare la trasformazione delle attestazioni GetLocalizedStringsTransformation:To use the GetLocalizedStringsTransformation claims transformation:

1. Definire una stringa di [localizzazione](localization.md) e associarla a un [profilo tecnico auto-asserito](self-asserted-technical-profile.md).
1. `ElementType` L'elemento `LocalizedString` deve essere `GetLocalizedStringsTransformationClaimType`impostato su .
1. Si `StringId` tratta di un identificatore univoco definito e utilizzato in un secondo momento nella trasformazione delle attestazioni.
1. Nella trasformazione delle attestazioni specificare l'elenco di attestazioni da impostare con la stringa localizzata. Il `ClaimTypeReferenceId` è un riferimento a un ClaimType già definito nella sezione ClaimsSchema nei criteri. Il `TransformationClaimType` è il nome della stringa `StringId` localizzata `LocalizedString` come definito nell'elemento.
1. In un [profilo tecnico auto-asserito](self-asserted-technical-profile.md)o in una trasformazione delle attestazioni di input o output del controllo di [visualizzazione,](display-controls.md) creare un riferimento alla trasformazione delle attestazioni.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

L'esempio seguente cerca l'oggetto del messaggio di posta elettronica, il corpo, il messaggio di codice e la firma del messaggio di posta elettronica dalle stringhe localizzate. Queste attestazioni successivamente utilizzate dal modello di verifica della posta elettronica personalizzato.

Definire stringhe localizzate per inglese (impostazione predefinita) e spagnolo.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

La trasformazione delle attestazioni imposta il valore `StringId` del tipo di attestazione *soggetto* con il valore del *email_subject*.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di output:
  - **oggetto**: Codice di verifica dell'e-mail dell'account Contoso
  - **messaggio**: Grazie per aver verificato il tuo account!
  - **codeIntro**: Il codice è
  - **firma**: Sinceramente


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
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
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
    - **restrictionValueClaim**: Non è possibile accedere perché si è minorenni.

## <a name="lookupvalue"></a>LookupValue

Esegue la ricerca di un valore di attestazione da un elenco di valori in base al valore di un'altra attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | Attestazione che contiene il valore di ricerca |
| InputParameter | |string | Raccolta di elementi inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Controlla se viene restituito un errore quando non esiste alcuna ricerca corrispondente. |
| OutputClaim | inputParameterId | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. Valore dell'oggetto `Id`corrispondente. |

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

Quando `errorOnFailedLookup` il parametro `true`di input è impostato su , la trasformazione delle attestazioni **LookupValue** viene sempre eseguita da un [profilo tecnico](validation-technical-profile.md) di convalida chiamato da un profilo [tecnico auto-asserito](self-asserted-technical-profile.md)o da un oggetto [DisplayConrtol](display-controls.md). I `LookupNotFound` metadati di un profilo tecnico auto-asserito controllano il messaggio di errore che viene presentato all'utente.

![Esecuzione di AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

L'esempio seguente cerca il nome di dominio in una delle raccolte inpuParameters. La trasformazione delle attestazioni cerca il nome di dominio nell'identificatore e ne restituisce il valore (un ID applicazione) o genera un messaggio di errore.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputParameterId**: live.com
- Parametri di input:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Errore:
    - Nessuna corrispondenza per il valore dell'attestazione di input nell'elenco di ID di parametro di input e errorOnFailedLookup è true.


## <a name="nullclaim"></a>NullClaim

Pulisce il valore di una determinata attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | Il valore dell'attestazione è impostato su NULL. |

Utilizzare questa trasformazione attestazione per rimuovere i dati non necessari dal contenitore delle proprietà delle attestazioni in modo che il cookie di sessione sarà più piccolo. L'esempio seguente rimuove il valore del tipo di attestazione `TermsOfService`.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Attestazioni di input:
    - **outputClaim**: Benvenuto nell'app Contoso. Se continui a esplorare e a usare questo sito Web, accetti di rispettare i termini e le condizioni seguenti...
- Attestazioni di output:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Ottiene la parte di dominio di un indirizzo di posta elettronica.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | Elemento ClaimType che contiene l'indirizzo di posta elettronica. |
| OutputClaim | dominio | string | Elemento ClaimType (dominio) generato dopo che è stata richiamata questa trasformazione di attestazioni. |

Usare questa trasformazione di attestazioni per analizzare il nome di dominio dopo il simbolo @ dell'utente. La trasformazione di attestazioni seguenti dimostra come analizzare il nome di dominio da un'attestazione **email**.

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
  - **emailAddress**:joe@outlook.com
- Attestazioni di output:
    - **domain**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatchSetClaimsIfRegexMatch

Verifica `claimToMatch` che un'attestazione di stringa e `matchTo` un parametro di `outputClaimIfMatched` input siano uguali e imposta le attestazioni `true` di `false` output con il valore presente nel parametro di input, insieme all'attestazione dell'output dei risultati di confronto, che deve essere impostata come o in base al risultato del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | Tipo dell'attestazione di cui eseguire il confronto. |
| InputParameter | matchTo | string | L'espressione regolare per cui cercare una corrispondenza. |
| InputParameter | outputClaimIfMatched | string | Valore da impostare se le stringhe sono uguali. |
| InputParameter | extractGroups (gruppi di dati) | boolean | [Facoltativo] Specifica se la corrispondenza Regex deve estrarre i valori dei gruppi. Valori possibili: `true` `false` , o (impostazione predefinita). | 
| OutputClaim | outputClaim | string | Se l'espressione regolare corrisponde, questa `outputClaimIfMatched` attestazione di output contiene il valore del parametro di input. O null, se nessuna corrispondenza. |
| OutputClaim | regexCompareResultClaim | boolean | Tipo di attestazione di output del risultato `true` `false` della corrispondenza dell'espressione regolare, che deve essere impostato come o in base al risultato della corrispondenza. |
| OutputClaim| Il nome della rivendicazione| string | Se il parametro di input extractGroups è impostato su true, è stato richiamato l'elenco dei tipi di attestazione generati dopo che è stata richiamata questa trasformazione delle attestazioni. Il nome dell'claimType deve corrispondere al nome del gruppo Regex. | 

### <a name="example-1"></a>Esempio 1

Controlla se il numero di telefono fornito è valido, in base al modello di espressione regolare del numero di telefono.

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Attestazioni di input:
    - **claimToMatch**: "64854114520"
- Parametri di input:
    - **matchTo**: "[0-9]{4,16}
    - **outputClaimIfMatched**: "isPhone"
- Attestazioni di output:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Esempio 2

Controlla se l'indirizzo di posta elettronica fornito è valido e restituisce l'alias di posta elettronica.

```XML
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Attestazioni di input:
    - **claimToMatch**:emily@contoso.com" "
- Parametri di input:
    - **matchTo**:`(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **extractGroups**: true
- Attestazioni di output:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica che un'attestazione di stringa e il parametro di input `matchTo` siano uguali e imposta le attestazioni di output con il valore presente nei parametri di input `stringMatchMsg` e `stringMatchMsgCode`, insieme all'attestazione di output del risultato di confronto che deve essere impostato come `true` o `false` in base al risultato del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo dell'attestazione di cui eseguire il confronto. |
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
    - **stringMatchMsgCode**: Le condizioni d'uso sono aggiornate alla versione 2
- Attestazioni di output:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: Le condizioni d'uso sono aggiornate alla versione 2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica che un'attestazione di stringa e il parametro di input `matchTo` siano uguali e imposta le attestazioni di output con il valore presente nel parametro di input `outputClaimIfMatched`, insieme all'attestazione di output del risultato di confronto che deve essere impostato come `true` or `false` in base al risultato del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | Tipo dell'attestazione di cui eseguire il confronto. |
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
    - **claimToMatch**: Minor
- Parametri di input:
    - **matchTo**: Minor
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**:  B2C_V1_90001
- Attestazioni di output:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>StringContains

Determinare se una sottostringa specificata si trova all'interno dell'attestazione di input. Il risultato è un nuovo elemento ClaimType booleano con il valore `true` o `false`. `true`se il parametro value si `false`trova all'interno di questa stringa, in caso contrario, .

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione, che deve essere cercato. |
|InputParameter|contains|string|Valore da cercare.|
|InputParameter|ignoreCase|string|Specifica se questo confronto deve ignorare la distinzione tra maiuscole e minuscole della stringa confrontata.|
| OutputClaim | outputClaim | string | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. Un indicatore booleano se la sottostringa si verifica all'interno dell'attestazione di input. |

Usare questa trasformazione delle attestazioni per verificare se un tipo di attestazione stringa contiene una sottostringa. Nell'esempio riportato `roles` di seguito viene verificato se il tipo di attestazione stringa contiene il valore di **admin**.

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**: "Amministratore, responsabile approvazione, editor"
- Parametri di input:
    - **contiene**: "admin",
    - **ignoreCase**: true
- Attestazioni di output:
    - **outputClaim**: true

## <a name="stringsubstring"></a>Sottostringa String

Estrae parti di un tipo di attestazione stringa, a partire dal carattere nella posizione specificata e restituisce il numero di caratteri specificato.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione, che contiene la stringa. |
| InputParameter | startIndex | INT | Posizione iniziale in base zero del carattere di una sottostringa in questa istanza. |
| InputParameter | length | INT | Numero di caratteri nella sottostringa. |
| OutputClaim | outputClaim | boolean | Stringa equivalente alla sottostringa di lunghezza che inizia in startIndex in questa istanza oppure Empty se startIndex è uguale alla lunghezza di questa istanza e length è zero. |

Ad esempio, ottenere il prefisso del paese del numero di telefono.


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**: "1644114520"
- Parametri di input:
    - **startIndex**: 0
    - **lunghezza**: 2
- Attestazioni di output:
    - **outputClaim**: "1"

## <a name="stringreplace"></a>Sostituzione di stringheStringReplace

Cerca un valore specificato in una stringa di tipo di attestazione e restituisce una nuova stringa di tipo di attestazione in cui tutte le occorrenze di una stringa specificata nella stringa corrente vengono sostituite con un'altra stringa specificata.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione, che contiene la stringa. |
| InputParameter | oldValue | string | Stringa da cercare. |
| InputParameter | newValue | string | La stringa per sostituire tutte le occorrenze di`oldValue` |
| OutputClaim | outputClaim | boolean | Stringa equivalente alla stringa corrente, ad eccezione del fatto che tutte le istanze di oldValue vengono sostituite con newValue. Se oldValue non viene trovato nell'istanza corrente, il metodo restituisce l'istanza corrente invariata. |

Ad esempio, normalizzare un numero di `-` telefono, rimuovendo i caratteri


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**: "164-411-452-054"
- Parametri di input:
    - **oldValue**: "-"
    - **lunghezza**: ""
- Attestazioni di output:
    - **outputClaim**: "164411452054"

## <a name="stringjoin"></a>StringJoin (Connessione stringhe)

Concatena gli elementi di un tipo di attestazione di raccolta di stringhe specificato, utilizzando il separatore specificato tra ogni elemento o membro.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Raccolta che contiene le stringhe da concatenare. |
| InputParameter | delimiter | string | Stringa da utilizzare come separatore, `,`ad esempio virgola . |
| OutputClaim | outputClaim | string | Stringa costituita dai membri `inputClaim` della raccolta di stringhe, delimitati dal `delimiter` parametro di input. |

Nell'esempio seguente viene eseguita una raccolta di stringhe di ruoli utente e viene convertita in una stringa delimitatore virgola. È possibile usare questo metodo per archiviare una raccolta di stringhe nell'account utente di Azure AD. Successivamente, quando si legge l'account `StringSplit` dalla directory, utilizzare il per convertire la stringa delimitatore virgola di nuovo in raccolta di stringhe.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputClaim**: [ "Admin", "Author", "Reader" ]
- Parametri di input:
  - **delimitatore**: ","
- Attestazioni di output:
  - **outputClaim**: "Admin,Author,Reader"


## <a name="stringsplit"></a>Stringa Divisa

Restituisce una matrice di stringhe che contiene le sottostringhe in questa istanza delimitate da elementi di una stringa specificata.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione stringa che contiene le sottostringhe da dividere. |
| InputParameter | delimiter | string | Stringa da utilizzare come separatore, `,`ad esempio virgola . |
| OutputClaim | outputClaim | stringCollection | Raccolta di stringhe i cui elementi contengono le sottostringhe in questa stringa delimitate dal `delimiter` parametro di input. |

Nell'esempio seguente viene eseguito un delimitatore di virgola di ruoli utente e viene convertita in una raccolta di stringhe.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **inputClaim**: "Admin,Author,Reader"
- Parametri di input:
  - **delimitatore**: ","
- Attestazioni di output:
  - **outputClaim**: [ "Admin", "Author", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>Espressioni di trasformazione attestazione stringaString claim transformations expressions
Le espressioni di trasformazione delle attestazioni nei criteri personalizzati di Azure AD B2C forniscono informazioni sul contesto relative all'ID tenant e all'ID profilo tecnico.

  | Expression | Descrizione | Esempio |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Nome technical profileId. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | ID del tenant dei criteri della relying party. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | ID del tenant del framework attendibilità. | your-tenant.onmicrosoft.com |
