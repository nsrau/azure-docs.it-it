---
title: Localizzazione-Azure Active Directory B2C
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
ms.openlocfilehash: 69368ecd7234912bcaf5eb606545f62ddb7b30a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204184"
---
# <a name="localization"></a>Localizzazione

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **Localizzazione** consente di supportare più impostazioni locali o lingue nei criteri per i percorsi utente. Il supporto di localizzazione nei criteri consente di:

- Configurare l'elenco esplicito delle lingue supportate di un criterio e selezionare una lingua predefinita.
- Fornire stringhe specifiche della lingua e raccolte.

```xml
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
| Abilitato | No | I valori possibili sono: `true` o `false`. |

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
| Item | 0:n | Definisce un'opzione disponibile per l'utente da selezionare per un'attestazione nell'interfaccia utente, ad esempio, un valore in un elenco a discesa. |

L'elemento **Item** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Text | Sì | La stringa di visualizzazione intuitiva che deve essere visualizzata dall'utente nell'interfaccia utente per questa opzione. |
| valore | Sì | Il valore di attestazione della stringa associato alla selezione di questa opzione. |
| SelectByDefault | No | Indica se questa opzione deve essere selezionata o meno per impostazione predefinita nell'interfaccia utente. I valori possibili sono: True o False. |

L'esempio seguente illustra l'uso dell'elemento **LocalizedCollections**. Contiene due elementi **LocalizedCollection**, uno per l'inglese e un altro per lo spagnolo. Entrambi impostano la raccolta **Restriction** dell'attestazione `Gender` con un elenco di elementi per l'inglese e lo spagnolo.

```xml
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
| ElementType | Sì | Valori possibili: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [predicate](#predicate), [InputValidation](#inputvalidation)o [UxElement](#uxelement).   | 
| ElementId | Sì | Se **elementType** è impostato su `ClaimType` , `Predicate` o `InputValidation` , questo elemento contiene un riferimento a un tipo di attestazione già definito nella sezione ClaimsSchema. |
| ID stringa | Sì | Se **ElementType** è impostato su `ClaimType`, questo elemento contiene un riferimento a un attributo di un tipo di attestazione. I valori possibili sono: `DisplayName`, `AdminHelpText` o `PatternHelpText`. Il valore`DisplayName` viene usato per impostare il nome visualizzato di attestazione. Il valore `AdminHelpText` viene usato per impostare il nome del testo di istruzioni dell'utente di attestazione. Il valore`PatternHelpText` viene usato per impostare il testo di istruzioni del criterio di attestazione. Se **ElementType** è impostato su `UxElement`, questo elemento contiene un riferimento a un elemento dell'interfaccia utente. Se **ElementType** è impostato su `ErrorMessage`, questo elemento specifica l'identificatore di un messaggio di errore. Visualizzare gli [ID stringa di localizzazione](localization-string-ids.md) per un elenco completo degli `UxElement` identificatori.|

## <a name="elementtype"></a>ElementType

Riferimento ElementType a un tipo di attestazione, a una trasformazione di attestazione o a un elemento dell'interfaccia utente nel criterio da localizzare.

| Elemento da localizzare | ElementType | ElementId |ID stringa |
| --------- | -------- | ----------- |----------- |
| Nome del provider di identità |`ClaimsProvider`| | ID dell'elemento ClaimsExchange|
| Attributi del tipo di attestazione|`ClaimType`|Nome del tipo di attestazione| Attributo dell'attestazione da localizzare. Valori possibili: `AdminHelpText` , `DisplayName` , `PatternHelpText` e `UserHelpText` .|
|Messaggio di errore|`ErrorMessage`||ID del messaggio di errore |
|Copia le stringhe localizzate in attestazioni|`GetLocalizedStringsTra nsformationClaimType`||Nome dell'attestazione di output|
|Messaggio utente predicato|`Predicate`|Nome del predicato| Attributo del predicato da localizzare. Valori possibili: `HelpText` .|
|Messaggio utente gruppo predicato|`InputValidation`|ID dell'elemento PredicateValidation.|ID dell'elemento PredicateGroup. Il gruppo predicato deve essere un elemento figlio dell'elemento validation Predicate come definito in ElementId.|
|Elementi dell'interfaccia utente |`UxElement` | | ID dell'elemento dell'interfaccia utente da localizzare.|

## <a name="examples"></a>Esempi

### <a name="claimsprovider"></a>ClaimsProvider

Il valore ClaimsProvider viene usato per localizzare uno dei provider di attestazioni nome visualizzato. 

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

Il valore ClaimType viene usato per localizzare uno degli attributi di attestazione. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Nell'esempio seguente viene illustrato come localizzare gli attributi DisplayName, UserHelpText e PatternHelpText del tipo di attestazione e-mail.

```xml
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


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Il valore GetLocalizedStringsTransformationClaimType viene usato per copiare le stringhe localizzate in attestazioni. Per altre informazioni, vedere [GetLocalizedStringsTransformation Claims Transformation](string-transformations.md#getlocalizedstringstransformation)


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

Il valore del predicato viene usato per localizzare uno dei messaggi di errore del [predicato](predicates.md) . 

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

Nell'esempio seguente viene illustrato come localizzare il testo della Guida per i predicati.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

Il valore InputValidation viene usato per localizzare uno dei messaggi di errore del gruppo [PredicateValidation](predicates.md) . 

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

Nell'esempio seguente viene illustrato come localizzare il testo della guida del gruppo di convalida del predicato.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

Il valore UxElement viene usato per localizzare uno degli elementi dell'interfaccia utente. Nell'esempio seguente viene illustrato come localizzare i pulsanti continua e Annulla.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Passaggi successivi

Per esempi di localizzazione, vedere gli articoli seguenti:

- [Personalizzazione della lingua con criteri personalizzati in Azure Active Directory B2C](custom-policy-localization.md)
- [Personalizzazione della lingua con i flussi utente in Azure Active Directory B2C](user-flow-language-customization.md)
