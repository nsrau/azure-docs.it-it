---
title: Predicates e PredicateValidations - Azure Active Directory B2C | Microsoft Docs
description: Esempi di trasformazione di attestazioni di account di social networking per lo schema del framework dell'esperienza di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06879164c6f72891b734da077c667c6f90448fe4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512957"
---
# <a name="predicates-and-predicatevalidations"></a>Predicates e PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Gli elementi **Predicates** e **PredicateValidations** consentono di eseguire un processo di convalida per assicurarsi che solo i dati adeguatamente formattati vengano immessi nel tenant di Azure Active Directory (Azure AD) B2C in uso.  

Il diagramma seguente mostra la relazione tra gli elementi:  

![Predicati](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicati  

L'elemento **Predicate** definisce una convalida di base per controllare il valore di un tipo di attestazione e restituisce `true` o `false`. La convalida viene eseguita usando un elemento **Method** specificato e un set di elementi **Parameter** pertinenti al metodo. Un predicato può ad esempio controllare se la lunghezza di un valore di attestazione di tipo stringa rientra nell'intervallo di parametri minimo e massimo specificato o se un valore di attestazione di tipo stringa contiene un set di caratteri. L'elemento **UserHelpText** visualizza un messaggio di errore agli utenti se il controllo ha esito negativo. Il valore dell'elemento **UserHelpText** può essere localizzato usando la [funzionalità di personalizzazione della lingua](localization.md).

L'elemento **Predicates** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Predicate | 1:n | Elenco di predicati. | 

L'elemento **Predicate** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| ID | Yes | Identificatore usato per il predicato. Altri elementi possono usare questo identificatore nei criteri. |
| Metodo | Yes | Tipo di metodo da usare per la convalida. Valori possibili: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters** e **IsDateRange**. Il valore **IsLengthRange** controlla se la lunghezza del valore di un'attestazione di tipo stringa rientra nell'intervallo di parametri minimo e massimo specificato. Il valore **MatchesRegex** controlla se il valore di un'attestazione di tipo stringa corrisponde a un'espressione regolare. Il valore **IncludesCharacters** controlla se il valore di un'attestazione di tipo stringa contiene un set di caratteri. Il valore **IsDateRange** controlla se il valore di un'attestazione di tipo data rientra nell'intervallo di parametri minimo e massimo specificato. |

L'elemento **Predicate** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Messaggio di errore per gli utenti se il controllo ha esito negativo. Questa stringa può essere localizzata usando la [funzionalità di personalizzazione della lingua](localization.md). |
| Parametri | 1:1 | Parametri del tipo di metodo della convalida della stringa. | 

L'elemento **Parameters** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Parametro | 1:n | Parametri del tipo di metodo della convalida della stringa. | 

L'elemento **Parameter** contiene gli attributi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| ID | 1:1 | Identificatore del parametro. |

L'esempio seguente mostra un metodo `IsLengthRange` con i parametri `Minimum` e `Maximum` che specificano l'intervallo di lunghezza della stringa:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

L'esempio seguente mostra un metodo `MatchesRegex` con il parametro `RegularExpression` che specifica un'espressione regolare:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

L'esempio seguente mostra un metodo `IncludesCharacters` con il parametro `CharacterSet` che specifica il set di caratteri:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

L'esempio seguente mostra un metodo `IsDateRange` con i parametri `Minimum` e `Maximum` che specificano l'intervallo di date con un formato `yyyy-MM-dd` e `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

Mentre i predicati definiscono la convalida da controllare rispetto a un tipo di attestazione, gli elementi **PredicateValidations** raggruppano un set di predicati per formare la convalida di un input utente che può essere applicata a un tipo di attestazione. Ogni elemento **PredicateValidation** contiene un set di elementi **PredicateGroup** che contengono un set di elementi **PredicateReference** che punta a un elemento **Predicate**. Per passare la convalida, il valore dell'attestazione deve superare tutti i testi di qualsiasi predicato in tutto il **PredicateGroup** con i rispettivi set di elementi **PredicateReference**.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

L'elemento **PredicateValidations** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | Elenco di convalida del predicato. | 

L'elemento **PredicateValidation** contiene l'attributo seguente:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| ID | Yes | Identificatore che viene usato per la convalida del predicato. L'elemento **ClaimType** può usare questo identificatore nei criteri. |

L'elemento **PredicateValidation** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | Elenco di gruppi di predicati. | 

L'elemento **PredicateGroups** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | Elenco di predicati. | 

L'elemento **PredicateGroup** contiene l'attributo seguente:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| ID | Yes | Identificatore usato per il gruppo di predicati.  |

L'elemento **PredicateGroup** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Descrizione del predicato che può essere utile agli utenti per sapere quale valore devono digitare. | 
| PredicateReferences | 1:n | Elenco di riferimenti del predicato. | 

L'elemento **PredicateReferences** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| MatchAtLeast | No | Specifica che il valore deve corrispondere almeno a quello di molte definizioni di predicato affinché l'input possa essere accettato. |

L'elemento **PredicateReferences** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | Riferimento a un predicato. | 

L'elemento **PredicateReference** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| ID | Yes | Identificatore che viene usato per la convalida del predicato.  |


## <a name="configure-password-complexity"></a>Configurare la complessità delle password

Con gli elementi **Predicates** e **PredicateValidationsInput** è possibile controllare i requisiti di complessità delle password specificate da un utente durante la creazione di un account. Per impostazione predefinita, Azure AD B2C usa password complesse. Azure AD B2C supporta anche opzioni di configurazione per controllare la complessità delle password che i clienti possono usare. È possibile definire la complessità delle password usando questi elementi predicato: 

- **IsLengthBetween8And64** con il metodo `IsLengthRange` convalida che la password deve contenere da 8 a 64 caratteri.
- **Lowercase** con il metodo `IncludesCharacters`, convalida che la password contiene una lettera minuscola.
- **Uppercase** con il metodo `IncludesCharacters` convalida che la password contiene una lettera maiuscola.
- **Number** con il metodo `IncludesCharacters` convalida che la password contiene una cifra.
- **Symbol** con il metodo `IncludesCharacters` convalida che la password contiene uno dei seguenti simboli `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN** con il metodo `MatchesRegex` convalida che la password contiene solo numeri.
- **AllowedAADCharacters** con il metodo `MatchesRegex` convalida che la password è costituita solo da caratteri non validi.
- **DisallowedWhitespace** con il metodo `MatchesRegex` convalida che la password non inizia o non finisce con un carattere di spazio.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Dopo aver definito le convalide di base, è possibile combinarle e creare un set di criteri di password da usare nei propri criteri:

- **SimplePassword** convalida DisallowedWhitespace, AllowedAADCharacters e IsLengthBetween8And64.
- **StrongPassword** convalida DisallowedWhitespace, AllowedAADCharacters e IsLengthBetween8And64. L'ultimo gruppo `CharacterClasses` esegue un set di predicati aggiuntivo con `MatchAtLeast` impostato su 3. La password dell'utente deve essere compresa tra 8 e 16 caratteri e includere tre dei tipi di caratteri seguenti: lettere minuscole, lettere maiuscole, numeri o simboli.
- **CustomPassword** convalida solo DisallowedWhitespace e AllowedAADCharacters. L'utente può pertanto specificare una password di qualsiasi lunghezza, purché i caratteri siano validi.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Nel tipo di attestazione aggiungere l'elemento **PredicateValidationReference** e specificare l'identificatore come una delle convalide di predicato, ad esempio SimplePassword, StrongPassword o CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Di seguito viene illustrato come vengono organizzati gli elementi quando Azure AD B2C visualizza il messaggio di errore:

![Processo dei predicati](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Configurare un intervallo di date

Con gli elementi **Predicates** e **PredicateValidations** è possibile controllare i valori di data minimo e massimo di **UserInputType** usando un elemento `DateTimeDropdown`. Creare a tale scopo un elemento **Predicate** con il metodo `IsDateRange` e specificare i parametri minimo e massimo.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Aggiungere un elemento **PredicateValidation** con un riferimento al predicato `DateRange`.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Nel tipo di attestazione aggiungere l'elemento **PredicateValidationReference** e specificare l'identificatore come `CustomDateRange`. 
    
```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
