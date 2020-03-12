---
title: Localizzazione-Azure Active Directory B2C
description: Specificare l'elemento di localizzazione di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126749"
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
| ElementType | Sì | Un riferimento a un elemento del tipo di attestazione o a un elemento dell'interfaccia utente nel criterio. Valori possibili: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`o `GetLocalizedStringsTransformationClaimType`. Il valore `ClaimType` viene usato per localizzare uno degli attributi di attestazione, come specificato nell'ID stringa. Il valore `UxElement` viene usato per localizzare uno degli elementi dell'interfaccia utente, come specificato nell'ID stringa. Il valore `ErrorMessage` viene usato per localizzare uno dei messaggi di errore del sistema, come specificato nell'ID stringa. Il valore `Predicate` viene usato per localizzare uno dei messaggi di errore del sistema [Predicato](predicates.md), come specificato nell'ID stringa. Il valore `InputValidation` viene usato per localizzare uno dei messaggi di errore del gruppo [PredicateValidation](predicates.md), come specificato nell'ID stringa. Il valore `GetLocalizedStringsTransformationClaimType` viene utilizzato per copiare le stringhe localizzate in attestazioni. Per altre informazioni, vedere [GetLocalizedStringsTransformation Claims Transformation](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Sì | Se **ElementType** è impostato su `ClaimType`, `Predicate` o `InputValidation`, questo elemento contiene un riferimento a un tipo di attestazione già definito nella sezione ClaimsSchema. |
| ID stringa | Sì | Se **ElementType** è impostato su `ClaimType`, questo elemento contiene un riferimento a un attributo di un tipo di attestazione. I valori possibili sono: `DisplayName`, `AdminHelpText` o `PatternHelpText`. Il valore`DisplayName` viene usato per impostare il nome visualizzato di attestazione. Il valore `AdminHelpText` viene usato per impostare il nome del testo di istruzioni dell'utente di attestazione. Il valore`PatternHelpText` viene usato per impostare il testo di istruzioni del criterio di attestazione. Se **ElementType** è impostato su `UxElement`, questo elemento contiene un riferimento a un elemento dell'interfaccia utente. Se **ElementType** è impostato su `ErrorMessage`, questo elemento specifica l'identificatore di un messaggio di errore. Visualizzare gli [ID stringa di localizzazione](localization-string-ids.md) per un elenco completo degli `UxElement` identificatori.|


L'esempio seguente mostra una pagina di iscrizione localizzata. I primi tre valori**LocalizedString** impostano l'attributo di attestazione. Il terzo modifica il valore del pulsante Continua. L'ultimo modifica il messaggio di errore.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

L'esempio seguente mostra una versione localizzata di **UserHelpText** del **Predicato** con ID `IsLengthBetween8And64`. E una versione localizzata di **UserHelpText** del**PredicateGroup** con ID `CharacterClasses` dei **PredicateValidation** con ID `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Configurare la localizzazione

Questo articolo illustra come supportare più impostazioni locali o lingue nel criterio per i percorsi utente. Per la localizzazione sono necessari tre passaggi: configurare l'elenco esplicito delle lingue supportate, fornire stringhe e raccolte specifiche della lingua e modificare ContentDefinition per la pagina.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Configurare l'elenco esplicito delle lingue supportate

Nell'elemento **BuildingBlocks**, aggiungere l'elemento**Localizzazione** con l'elenco delle lingue supportate. Nell'esempio seguente viene illustrato come definire il supporto di localizzazione sia in inglese (impostazione predefinita) che in spagnolo:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Passaggi successivi

Per esempi di localizzazione, vedere gli articoli seguenti:

- [Personalizzazione della lingua con criteri personalizzati in Azure Active Directory B2C](custom-policy-localization.md)
- [Personalizzazione della lingua con i flussi utente in Azure Active Directory B2C](user-flow-language-customization.md)
