---
title: Localizzazione - Azure Active Directory B2C
description: Specificare l'elemento di localizzazione di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681407"
---
# <a name="localization"></a>Localizzazione

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **Localizzazione** consente di supportare più impostazioni locali o lingue nei criteri per i percorsi utente. Il supporto di localizzazione nei criteri consente di:

- Configurare l'elenco esplicito delle lingue supportate di un criterio e selezionare una lingua predefinita.
- Fornire stringhe specifiche della lingua e raccolte.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

L'elemento **Localizzazione** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Attivato | No | I valori possibili sono: `true` o `false`. |

L'elemento **Localizazzione**contiene gli elementi XML seguenti

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | elenco delle lingue supportate. |
| LocalizedResources | 0:n | Elenco delle risorse localizzate. |

## <a name="supportedlanguages"></a>SupportedLanguages

L'elemento **SupportedLanguages** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| DefaultLanguage | Sì | La lingua predefinita da usare per le risorse localizzate. |
| MergeBehavior | No | Un valore di enumerazione dei valori che vengono uniti a qualsiasi ClaimType presente in un criterio padre con lo stesso identificatore. Usare questo attributo quando si sovrascrive un'attestazione specificata nei criteri di base. I valori possibili sono: `Append`, `Prepend` o `ReplaceAll`. Il valore `Append` specifica che la raccolta dei dati presenti deve essere accodata alla fine della raccolta specificata nel criterio padre. Il valore `Prepend` specifica che la raccolta dei dati presenti deve essere accodata alla fine della raccolta specificata nel criterio padre. Il valore `ReplaceAll` specifica che la raccolta di dati definita nel criterio padre deve essere ignorata e di usare i dati definiti nel criterio corrente. |

### <a name="supportedlanguages"></a>SupportedLanguages

L'elemento **SupportedLanguages** contiene gli attributi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Visualizza il contenuto che è conforme a un tag di linguaggio per RFC 5646 - Tag pe l'identificazione delle lingue. |

## <a name="localizedresources"></a>LocalizedResources

L'elemento **LocalizedResources** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ID | Sì | Identificatore usato per identificare unicamente le risorse localizzate. |

L'elemento **LocalizedResources** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Definisce le intere raccolte in diverse impostazioni cultura. Una raccolta può avere un numero diverso di elementi e stringhe diverse per diverse impostazioni cultura. Raccolte di esempi include le enumerazioni visualizzate nei tipi di attestazione. Ad esempio, viene visualizzato dall'utente una lista per paese/area geografica in un elenco a discesa. |
| LocalizedStrings | 0:n | Definisce tutte le stringhe, ad eccezione delle stringhe che vengono visualizzate nelle raccolte, in diverse impostazioni cultura. |

### <a name="localizedcollections"></a>LocalizedCollections

L'elemento **LocalizedResources** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | elenco delle lingue supportate. |

#### <a name="localizedcollection"></a>LocalizedCollection

L'elemento **LocalizedCollection** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ElementType | Sì | Fa riferimento a un elemento ClaimType o un elemento dell'interfaccia utente nel file dei criteri. |
| ElementId | Sì | Una stringa che contiene un riferimento a un tipo di attestazione già definito nella sezione che viene usata se **ElementType** è impostato su un ClaimType. |
| TargetCollection | Sì | La raccolta di destinazione. |

L'elemento **LocalizedCollection** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Elemento | 0:n | Definisce un'opzione disponibile per l'utente da selezionare per un'attestazione nell'interfaccia utente, ad esempio, un valore in un elenco a discesa. |

L'elemento **Item** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Text | Sì | La stringa di visualizzazione intuitiva che deve essere visualizzata dall'utente nell'interfaccia utente per questa opzione. |
| valore | Sì | Il valore di attestazione della stringa associato alla selezione di questa opzione. |
| SelectByDefault | No | Indica se questa opzione deve essere selezionata o meno per impostazione predefinita nell'interfaccia utente. I valori possibili sono: True o False. |

L'esempio seguente illustra l'uso dell'elemento **LocalizedCollections**. Contiene due elementi **LocalizedCollection**, uno per l'inglese e un altro per lo spagnolo. Entrambi impostano la raccolta **Restriction** dell'attestazione `Gender` con un elenco di elementi per l'inglese e lo spagnolo.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

L'elemento **LocalizedStrings** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Una stringa localizzata. |

L'elemento **LocalizedString** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ElementType | Sì | Valori possibili: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predicate](#predicate), [InputValidation](#inputvalidation)o [UxElement](#uxelement).   | 
| ElementId | Sì | Se **ElementType** è `ClaimType` `Predicate`impostato `InputValidation`su , , o , questo elemento contiene un riferimento a un tipo di attestazione già definito nella sezione ClaimsSchema . |
| ID stringa | Sì | Se **ElementType** è impostato su `ClaimType`, questo elemento contiene un riferimento a un attributo di un tipo di attestazione. I valori possibili sono: `DisplayName`, `AdminHelpText` o `PatternHelpText`. Il valore`DisplayName` viene usato per impostare il nome visualizzato di attestazione. Il valore `AdminHelpText` viene usato per impostare il nome del testo di istruzioni dell'utente di attestazione. Il valore`PatternHelpText` viene usato per impostare il testo di istruzioni del criterio di attestazione. Se **ElementType** è impostato su `UxElement`, questo elemento contiene un riferimento a un elemento dell'interfaccia utente. Se **ElementType** è impostato su `ErrorMessage`, questo elemento specifica l'identificatore di un messaggio di errore. Visualizzare gli [ID stringa di localizzazione](localization-string-ids.md) per un elenco completo degli `UxElement` identificatori.|

## <a name="elementtype"></a>ElementType

Il riferimento ElementType a un tipo di attestazione, una trasformazione attestazione o un elemento dell'interfaccia utente nei criteri da localizzare.

| Elemento da localizzare | ElementType | ElementId |ID stringa |
| --------- | -------- | ----------- |----------- |
| Nome provider di identità |`ClaimsProvider`| | ID dell'elemento ClaimsExchange|
| Attributi del tipo di attestazione|`ClaimType`|Nome del tipo di attestazione| Attributo dell'attestazione da localizzare. Valori `AdminHelpText`possibili: `DisplayName` `PatternHelpText`, `UserHelpText`, , e .|
|Messaggio di errore|`ErrorMessage`||Id del messaggio di errore |
|Copia le stringhe localizzate nelle attestazioni|`GetLocalizedStringsTra nsformationClaimType`||Il nome dell'attestazione di output|
|Messaggio utente predicato|`Predicate`|Il nome del predicato| Attributo del predicato da localizzare. Valori possibili: `HelpText`.|
|Messaggio utente del gruppo di predicati|`InputValidation`|ID dell'elemento PredicateValidation.|ID dell'elemento PredicateGroup. Il gruppo di predicati deve essere un elemento figlio dell'elemento di convalida del predicato come definito nel ElementId.|
|Elementi dell'interfaccia utente |`UxElement` | | ID dell'elemento dell'interfaccia utente da localizzare.|

## <a name="examples"></a>Esempi

### <a name="claimsprovider"></a>ClaimsProvider

Il ClaimsProvider valore viene utilizzato per localizzare uno dei provider di attestazioni nome visualizzato. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

Nell'esempio seguente viene illustrato come localizzare il nome visualizzato dei provider di attestazioni.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

Il ClaimType valore viene utilizzato per localizzare uno degli attributi dell'attestazione. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Nell'esempio seguente viene illustrato come localizzare gli attributi DisplayName, UserHelpText e PatternHelpText del tipo di attestazione di posta elettronica.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

Il valore ErrorMessage viene utilizzato per localizzare uno dei messaggi di errore di sistema. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Nell'esempio seguente viene illustrato come localizzare il messaggio di errore UserMessageIfClaimsPrincipalAlreadyExists.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Il GetLocalizedStringsTransformationClaimType valore viene utilizzato per copiare le stringhe localizzate nelle attestazioni. Per altre informazioni, vedere [Trasformazione delle attestazioni GetLocalizedStringsTransformationFor](string-transformations.md#getlocalizedstringstransformation) more information, see GetLocalizedStringsTransformation claims transformation


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

Nell'esempio seguente viene illustrato come localizzare le attestazioni di output della trasformazione delle attestazioni GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicate

Il valore Predicate viene utilizzato per localizzare uno dei messaggi di errore [Predicate.](predicates.md) 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Nell'esempio seguente viene illustrato come localizzare il testo della Guida dei predicati.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

Il Valore InputValidation viene utilizzato per localizzare uno dei messaggi di errore del gruppo [PredicateValidation.](predicates.md) 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Nell'esempio seguente viene illustrato come localizzare un testo della Guida del gruppo di convalida del predicato.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>Oggetto UxElement

Il UxElement valore viene utilizzato per localizzare uno degli elementi dell'interfaccia utente. Nell'esempio seguente viene illustrato come localizzare i pulsanti continua e annulla.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per esempi di localizzazione:See the following articles for localization examples:

- [Personalizzazione della lingua con criteri personalizzati in Azure Active Directory B2C](custom-policy-localization.md)
- [Personalizzazione della lingua con flussi utente in Azure Active Directory B2C](user-flow-language-customization.md)
