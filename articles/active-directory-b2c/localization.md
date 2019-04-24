---
title: 'Localizzazione: Azure Active Directory B2C | Microsoft Docs'
description: Specificare l'elemento di localizzazione di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8f252b536c80ad997f3c0eb10b10d5cb8c330fc6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60401749"
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

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| Enabled | No  | I valori possibili sono: `true` o `false`. |

L'elemento **Localizazzione**contiene gli elementi XML seguenti

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | elenco delle lingue supportate. | 
| LocalizedResources | 0: n | Elenco delle risorse localizzate. |

## <a name="supportedlanguages"></a>SupportedLanguages

L'elemento **SupportedLanguages** contiene gli attributi seguenti:

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| DefaultLanguage | Sì | La lingua predefinita da usare per le risorse localizzate. |
| MergeBehavior | No  | Un valore di enumerazione dei valori che vengono uniti a qualsiasi ClaimType presente in un criterio padre con lo stesso identificatore. Usare questo attributo quando si sovrascrive un'attestazione specificata nei criteri di base. I valori possibili sono: `Append`, `Prepend` o `ReplaceAll`. Il valore `Append` specifica che la raccolta dei dati presenti deve essere accodata alla fine della raccolta specificata nel criterio padre. Il valore `Prepend` specifica che la raccolta dei dati presenti deve essere accodata alla fine della raccolta specificata nel criterio padre. Il valore `ReplaceAll` specifica che la raccolta di dati definita nel criterio padre deve essere ignorata e di usare i dati definiti nel criterio corrente. |

### <a name="supportedlanguages"></a>SupportedLanguages

L'elemento **SupportedLanguages** contiene gli attributi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Visualizza il contenuto che è conforme a un tag di linguaggio per RFC 5646 - Tag pe l'identificazione delle lingue. | 

## <a name="localizedresources"></a>LocalizedResources

L'elemento **LocalizedResources** contiene gli attributi seguenti:

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| ID | Sì | Identificatore usato per identificare unicamente le risorse localizzate. |

L'elemento **LocalizedResources** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definisce le intere raccolte in diverse impostazioni cultura. Una raccolta può avere un numero diverso di elementi e stringhe diverse per diverse impostazioni cultura. Raccolte di esempi include le enumerazioni visualizzate nei tipi di attestazione. Ad esempio, viene visualizzato dall'utente una lista per paese/area geografica in un elenco a discesa. |
| LocalizedStrings | 0: n | Definisce tutte le stringhe, ad eccezione delle stringhe che vengono visualizzate nelle raccolte, in diverse impostazioni cultura. |

### <a name="localizedcollections"></a>LocalizedCollections

L'elemento **LocalizedResources** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | elenco delle lingue supportate. |

#### <a name="localizedcollection"></a>LocalizedCollection

L'elemento **LocalizedCollection** contiene gli attributi seguenti:

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| ElementType | Sì | Fa riferimento a un elemento ClaimType o un elemento dell'interfaccia utente nel file dei criteri. |
| ElementId | Sì | Una stringa che contiene un riferimento a un tipo di attestazione già definito nella sezione che viene usata se **ElementType** è impostato su un ClaimType. |
| TargetCollection | Sì | La raccolta di destinazione. |

L'elemento **LocalizedCollection** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| Elemento | 0:n | Definisce un'opzione disponibile per l'utente da selezionare per un'attestazione nell'interfaccia utente, ad esempio, un valore in un elenco a discesa. |

L'elemento **Item** contiene gli attributi seguenti:

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| Text | Sì | La stringa di visualizzazione intuitiva che deve essere visualizzata dall'utente nell'interfaccia utente per questa opzione. |
| Value | Sì | Il valore di attestazione della stringa associato alla selezione di questa opzione. |

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

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Una stringa localizzata. |

L'elemento **LocalizedString** contiene gli attributi seguenti:

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| ElementType | Sì | Un riferimento a un elemento del tipo di attestazione o a un elemento dell'interfaccia utente nel criterio. I valori possibili sono: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate` o  . Il valore `ClaimType` viene usato per localizzare uno degli attributi di attestazione, come specificato nell'ID stringa. Il valore `UxElement` viene usato per localizzare uno degli elementi dell'interfaccia utente, come specificato nell'ID stringa. Il valore `ErrorMessage` viene usato per localizzare uno dei messaggi di errore del sistema, come specificato nell'ID stringa. Il valore `Predicate` viene usato per localizzare uno dei messaggi di errore del sistema [Predicato](predicates.md), come specificato nell'ID stringa. Il valore `InputValidation` viene usato per localizzare uno dei messaggi di errore del gruppo [PredicateValidation](predicates.md), come specificato nell'ID stringa. |
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

### <a name="provide-language-specific-strings-and-collections"></a>Fornire stringhe e raccolte specifiche della lingua 

Aggiungere gli elementi**LocalizedResources** all'interno dell'elemento **Localizzazione** una volta chiuso l'elemento **SupportedLanguages**. Si aggiungono elementi**LocalizedResources** per ogni pagina (definizione contenuto) e in qualsiasi lingua si desideri supportare. Per personalizzare la pagina di iscrizione o di accesso unificata, le pagine di iscrizione e di autenticazione a più fattori (MFA) in inglese, spagnolo e francese, aggiungere i seguenti elementi **LocalizedResources**.  
- Pagina unificata di iscrizione o di accesso, inglese `<LocalizedResources Id="api.signuporsignin.en">`
- Pagina unificata di iscrizione o di accesso, spagnolo `<LocalizedResources Id="api.signuporsignin.es">`
- Pagina unificata di iscrizione o di accesso, francese `<LocalizedResources Id="api.signuporsignin.fr">` 
- Iscrizione, inglese `<LocalizedResources Id="api.localaccountsignup.en">`
- Iscrizione, spagnolo `<LocalizedResources Id="api.localaccountsignup.es">`
- Iscrizione, francese `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, inglese `<LocalizedResources Id="api.phonefactor.en">`
- MFA, spagnolo `<LocalizedResources Id="api.phonefactor.es">`
- MFA, francese `<LocalizedResources Id="api.phonefactor.fr">`

Ogni elemento **LocalizedResources** contiene tutti gli elementi **LocalizedStrings** necessari con più elementi **LocalizedString** ed elementi **LocalizedCollections**con più elementi **LocalizedCollection**.  L'esempio seguente aggiunge la localizzazione in lingua inglese della pagina di iscrizione: 

Note: questo esempio fa riferimento ai tipi di attestazione `Gender` e `City`. Per usare questo esempio, assicurarsi che queste attestazioni siano definite. Per altre informazioni, vedere [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

La localizzazione della pagina di iscrizione in spagnolo.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Modificare ContentDefinition per la pagina 

Per ogni pagina che si desideri localizzare, specificare i codici della lingua da ricercare in **ContentDefinition**.

Nell'esempio seguente, le stringhe personalizzate in inglese (en) e spagnolo (es) vengono aggiunte alla pagina di iscrizione. Il **LocalizedResourcesReferenceId** per ogni **LocalizedResourcesReference** è lo stesso delle impostazioni locali, ma è possibile usare qualsiasi stringa come identificatore. Per ogni combinazione di lingua e di pagina, si sceglie il  corrispondente **LocalizedResources** creato in precedenza.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

L'esempio seguente mostra l'XML finale:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```




