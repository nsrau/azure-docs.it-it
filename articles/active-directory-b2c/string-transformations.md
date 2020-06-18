---
title: Esempi di trasformazione di attestazioni di stringa per criteri personalizzati
titleSuffix: Azure AD B2C
description: Esempi di trasformazione di attestazioni di stringa per lo schema IEF (Identity Experience Framework) di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c2291d4d2eca2abd11ef9c0f18f3fda52424ab93
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739091"
---
# <a name="string-claims-transformations"></a>Trasformazioni di attestazioni di stringa

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo contiene esempi per l'uso delle trasformazioni di attestazioni di stringa dello schema Identity Experience Framework in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Confronta due attestazioni e genera un'eccezione se non sono uguali in base agli elementi inputClaim1, inputClaim2 e stringComparison del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
| InputClaim | inputClaim2 | string | Tipo della seconda attestazione di cui eseguire il confronto. |
| InputParameter | stringComparison | string | Confronto tra le stringhe con valore Ordinal o OrdinalIgnoreCase. |

La trasformazione dell'attestazione **AssertStringClaimsAreEqual** viene sempre eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) chiamato da un [profilo tecnico autocertificato](self-asserted-technical-profile.md) o da un [controllo di visualizzazione](display-controls.md). I metadati `UserMessageIfClaimsTransformationStringsAreNotEqual` di un profilo tecnico autocertificato controllano il messaggio di errore visualizzato all'utente. I messaggi di errore possono essere [localizzati](localization-string-ids.md#claims-transformations-error-messages).


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
| InputClaim | inputClaim1 | string | ClaimType da modificare. |
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

Crea un'attestazione di stringa dal parametro di input specificato nella trasformazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
|----- | ----------------------- | --------- | ----- |
| InputParameter | Valore | string | Stringa da impostare. Il parametro di input supporta [espressioni di trasformazione di attestazioni di stringa](string-transformations.md#string-claim-transformations-expressions). |
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
| InputClaim | inputClaim1 | string | Tipo della prima attestazione di cui eseguire il confronto. |
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
| InputParameter | stringFormat | string | Formato della stringa, ad esempio il parametro {0}. Il parametro di input supporta [espressioni di trasformazione di attestazioni di stringa](string-transformations.md#string-claim-transformations-expressions).  |
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

Formatta due attestazioni in base alla stringa di formato specificata. Questa trasformazione usa il metodo C# `String.Format`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | Elemento ClaimType che funge come parametro {0} del formato della stringa. |
| InputClaim | inputClaim | string | Elemento ClaimType che funge come parametro {1} del formato della stringa. |
| InputParameter | stringFormat | string | Formato della stringa, ad esempio i parametri {0} e {1}. Il parametro di input supporta [espressioni di trasformazione di attestazioni di stringa](string-transformations.md#string-claim-transformations-expressions).   |
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

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Copia le stringhe localizzate in attestazioni.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | Nome della stringa localizzata. | string | Elenco dei tipi di attestazione generati dopo la chiamata della trasformazione di attestazioni. |

Per usare la trasformazione di attestazioni GetLocalizedStringsTransformation:

1. Definire una [stringa di localizzazione](localization.md) e associarla a un [profilo tecnico auto-certificato](self-asserted-technical-profile.md).
1. L'oggetto `ElementType` dell'elemento `LocalizedString` deve essere impostato su `GetLocalizedStringsTransformationClaimType`.
1. `StringId` è un identificatore univoco che viene definito e usato in un secondo momento nella trasformazione di attestazioni.
1. Nella trasformazione di attestazioni specificare l'elenco di attestazioni da impostare con la stringa localizzata. `ClaimTypeReferenceId` è un riferimento a un elemento ClaimType già definito nella sezione ClaimsSchema nei criteri. `TransformationClaimType` è il nome della striga localizzata come definito in `StringId` dell'elemento `LocalizedString`.
1. In una trasformazione di attestazioni di input o di output di un [profilo tecnico autocertificato](self-asserted-technical-profile.md) o di un [controllo di visualizzazione](display-controls.md), creare un riferimento alla trasformazione di attestazioni.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

Nell'esempio seguente vengono cercati l'oggetto, il corpo, il messaggio di codice e la firma del messaggio e-mail dalle stringhe localizzate. Tali attestazioni vengono usate in seguito dal modello di verifica della posta elettronica personalizzata.

Definire le stringhe localizzate per la lingua inglese (impostazione predefinita) e per lo spagnolo.

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

La trasformazione di attestazioni imposta il valore del tipo di attestazione *subject* sul valore *email_subject* di `StringId`.

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
  - **subject**: codice di verifica della posta elettronica dell'account Contoso
  - **message**: Grazie per aver verificato l'account.
  - **codeIntro**: Il tuo codice è
  - **signature**: Cordiali saluti


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
    - **restrictionValueClaim**: Accesso non consentito ai minorenni.

## <a name="lookupvalue"></a>LookupValue

Esegue la ricerca di un valore di attestazione da un elenco di valori in base al valore di un'altra attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | Attestazione che contiene il valore di ricerca |
| InputParameter | |string | Raccolta di elementi inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Controlla se viene restituito un errore quando non esiste alcuna ricerca corrispondente. |
| OutputClaim | inputParameterId | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. Valore dell'ID corrispondente `Id`. |

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
    - **outputClaim**:    c7026f88-4299-4cdb-965d-3f166464b8a9

Quando il parametro di input `errorOnFailedLookup` è impostato su `true`, la trasformazione di attestazioni **LookupValue** viene sempre eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) chiamato da un [profilo tecnico autocertificato](self-asserted-technical-profile.md) o da un [controllo di visualizzazione](display-controls.md). I metadati `LookupNotFound` di un profilo tecnico autocertificato controllano il messaggio di errore visualizzato all'utente.

![Esecuzione di AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

L'esempio seguente cerca il nome di dominio in una delle raccolte inpuParameters. La trasformazione di attestazioni esegue la ricerca del nome di dominio nell'identificatore e ne restituisce il valore (ID applicazione) oppure genera un messaggio di errore.

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
    - Non sono state trovate corrispondenze per il valore dell'attestazione di input nell'elenco di ID parametro di input e il valore di errorOnFailedLookup è true.


## <a name="nullclaim"></a>NullClaim

Pulisce il valore di una determinata attestazione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | Il valore dell'attestazione è impostato su NULL. |

Usare questa trasformazione di attestazione per rimuovere i dati non necessari dal contenitore delle proprietà delle attestazioni, in modo che il cookie di sessione sia minore. L'esempio seguente rimuove il valore del tipo di attestazione `TermsOfService`.

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
  - **emailAddress**: joe@outlook.com
- Attestazioni di output:
    - **domain**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Verifica che un'attestazione di stringa e il parametro di input `claimToMatch` siano uguali e imposta le attestazioni di output con il valore presente nel parametro di input `matchTo`, insieme all'attestazione di output del risultato di confronto che deve essere impostato come `outputClaimIfMatched``true`or`false`in base al risultato del confronto.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | Tipo dell'attestazione di cui eseguire il confronto. |
| InputParameter | matchTo | string | Espressione regolare in base a cui trovare una corrispondenza. |
| InputParameter | outputClaimIfMatched | string | Valore da impostare se le stringhe sono uguali. |
| InputParameter | extractGroups | boolean | [Facoltativo] Specifica se la corrispondenza regex deve estrarre i valori dei gruppi. Valori possibili: `true` o `false` (impostazione predefinita). | 
| OutputClaim | outputClaim | string | Se l'espressione regolare corrisponde, l'attestazione di output contiene il valore del parametro di input `outputClaimIfMatched`. Se non è presente alcuna corrispondenza, il valore è Null. |
| OutputClaim | regexCompareResultClaim | boolean | Tipo di attestazione di output del risultato della corrispondenza di espressione regolare, che deve essere impostato come `true` o `false` in base al risultato della corrispondenza. |
| OutputClaim| Nome dell'attestazione| string | Se il parametro di input extractGroups è impostato su true, viene richiamato l'elenco dei tipi di attestazione generati dopo la trasformazione di attestazioni. Il nome dell'elemento claimType deve corrispondere al nome del gruppo regex. | 

### <a name="example-1"></a>Esempio 1

Verifica se il numero di telefono indicato è valido, in base al modello di espressione regolare del numero di telefono.

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
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**:  "isPhone"
- Attestazioni di output:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Esempio 2

Verifica se l'indirizzo e-mail specificato è valido e restituisce l'alias di posta elettronica.

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
    - **claimToMatch**: "emily@contoso.com"
- Parametri di input:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**:  "isEmail"
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
    - **stringMatchMsgCode**:  Il tipo di servizio è aggiornato alla versione 2
- Attestazioni di output:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: Il tipo di servizio è aggiornato alla versione 2
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
    - **claimToMatch**: Minorenne
- Parametri di input:
    - **matchTo**: Minorenne
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**:  B2C_V1_90001
- Attestazioni di output:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>StringContains

Determina se una substring specificata si trova all'interno dell'attestazione di input. Il risultato è un nuovo elemento ClaimType booleano con il valore `true` o `false`. `true` se il parametro del valore si trova all'interno di questa stringa; in caso contrario `false`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione in cui eseguire la ricerca. |
|InputParameter|contains|string|Valore da cercare.|
|InputParameter|ignoreCase|string|Specifica se il confronto deve ignorare l'uso di maiuscole e minuscole nelle stringhe da confrontare.|
| OutputClaim | outputClaim | string | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. Indicatore booleano se la substring si trova all'interno dell'attestazione di input. |

Usare questa trasformazione di attestazioni per verificare se un tipo di attestazione di stringa contiene una substring. Nell'esempio seguente viene verificato se il tipo di attestazione di stringa `roles` contiene il valore **admin**.

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
    - **inputClaim**: "Admin, Approver, Editor"
- Parametri di input:
    - **contains**: "admin,"
    - **ignoreCase**: true
- Attestazioni di output:
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

Estrae parti di un tipo di attestazione di stringa, a partire dal carattere in corrispondenza della posizione specificata, e restituisce il numero di caratteri specificato.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione che contiene la stringa. |
| InputParameter | startIndex | INT | Posizione iniziale in base zero del carattere di una substring in questa istanza. |
| InputParameter | length | INT | Numero di caratteri nella substring. |
| OutputClaim | outputClaim | boolean | Stringa equivalente alla substring di lunghezza a partire dalla startIndex in questa istanza oppure Empty se startIndex è uguale alla lunghezza di questa istanza e la lunghezza è zero. |

Ottenere ad esempio il prefisso del paese/area geografica del numero di telefono.


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
    - **inputClaim**: "+1644114520"
- Parametri di input:
    - **startIndex**: 0
    - **length**:  2
- Attestazioni di output:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>StringReplace

Cerca un valore specificato in una stringa del tipo di attestazione e restituisce una nuova stringa del tipo di attestazione in cui tutte le occorrenze di una stringa specificata in quella corrente vengono sostituite con un'altra stringa specificata.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione che contiene la stringa. |
| InputParameter | oldValue | string | Stringa da cercare |
| InputParameter | newValue | string | Stringa con cui sostituire tutte le occorrenze di `oldValue` |
| OutputClaim | outputClaim | boolean | Stringa equivalente alla stringa corrente, ad eccezione del fatto che tutte le istanze di oldValue vengono sostituite con newValue. Se oldValue non viene trovato nell'istanza corrente, il metodo restituisce l'istanza corrente invariata. |

Normalizzare ad esempio un numero di telefono rimuovendo i caratteri `-`


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
    - **inputClaim**: "+164-411-452-054"
- Parametri di input:
    - **oldValue**: "-"
    - **length**:  ""
- Attestazioni di output:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin

Concatena gli elementi di un tipo di attestazione della raccolta di stringhe specificato usando il separatore specificato tra ogni elemento o membro.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Raccolta che contiene le stringhe da concatenare. |
| InputParameter | delimiter | string | Stringa da utilizzare come separatore, ad esempio virgola `,`. |
| OutputClaim | outputClaim | string | Stringa costituita dai membri della raccolta di stringhe `inputClaim`, delimitata dal parametro di input `delimiter`. |

L'esempio seguente accetta una raccolta di stringhe di ruoli utente e la converte in una stringa con delimitatore virgola. È possibile usare questo metodo per archiviare una raccolta di stringhe nell'account utente di Azure AD. In seguito, quando l'account viene letto dalla directory, usare `StringSplit` per convertire nuovamente la stringa con delimitatore virgola nella raccolta di stringhe.

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
  - **delimiter**: ","
- Attestazioni di output:
  - **outputClaim**: "Admin,Author,Reader"


## <a name="stringsplit"></a>StringSplit

Restituisce una matrice di stringhe contenente le sottostringhe di questa stringa delimitate dagli elementi di una stringa specificata.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Tipo di attestazione di stringa che contiene le sottostringhe da dividere. |
| InputParameter | delimiter | string | Stringa da utilizzare come separatore, ad esempio virgola `,`. |
| OutputClaim | outputClaim | stringCollection | Raccolta di stringhe i cui elementi contengono le sottostringhe in questa stringa delimitate dal parametro di input `delimiter`. |

L'esempio seguente accetta una stringa di ruoli utente con delimitatore virgola e la converte in una raccolta di stringhe.

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
  - **delimiter**: ","
- Attestazioni di output:
  - **outputClaim**: [ "Admin", "Author", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>Espressioni di trasformazioni di attestazioni di stringa
Le espressioni di trasformazioni di attestazioni nei criteri personalizzati di Azure AD B2C forniscono informazioni di contesto sull'ID tenant e sull'ID profilo tecnico.

  | Expression | Descrizione | Esempio |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Nome profileId tecnico. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | ID del tenant dei criteri della relying party. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | ID del tenant del framework attendibilità. | your-tenant.onmicrosoft.com |
