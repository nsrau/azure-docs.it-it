---
title: Complessità delle password nei criteri personalizzati - Azure AD B2C | Microsoft Docs
description: Come configurare i requisiti di complessità delle password nei criteri personalizzati
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 4dabcdef32d337c5063d2b0503f32b06d5d0fdeb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="configure-password-complexity-in-custom-policies"></a>Configurare la complessità delle password nei criteri personalizzati

> [!NOTE]
> **Questa funzionalità è in anteprima.**  Contattare [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) per abilitare questa funzionalità per il proprio tenant di prova.  Non testare questa funzionalità in un tenant di produzione.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo descrive in modo approfondito come funziona la complessità delle password e come abilitarla con i criteri personalizzati di Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Configurare i requisiti di complessità delle password

Azure Active Directory B2C (Azure AD B2C) supporta la modifica dei requisiti di complessità delle password specificate da un utente finale durante la creazione di un account.  Per impostazione predefinita, Azure AD B2C usa password di complessità **Alta**.  Azure AD B2C supporta anche opzioni di configurazione per controllare la complessità delle password che i clienti possono usare.  Questo articolo illustra come configurare la complessità delle password nei criteri personalizzati.  È possibile anche usare [Configurare la complessità delle password nei criteri predefiniti](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>prerequisiti

Un tenant di Azure AD B2C configurato per completare una procedura di iscrizione/accesso di un account locale, come descritto in [Introduzione](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Come configurare la complessità delle password nei criteri personalizzati

Per configurare la complessità delle password nei criteri personalizzati, è necessario che la struttura generale dei criteri personalizzati includa elementi `ClaimsSchema`, `Predicates` e `InputValidations` all'interno di `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Di seguito viene descritto lo scopo di questi elementi:

- Ogni elemento `Predicate` definisce un controllo di convalida delle stringhe di base che restituisce true o false.
- L'elemento `InputValidations` contiene uno o più elementi `InputValidation`.  Ogni `InputValidation` viene costruito usando una serie di elementi `Predicate`, che consentono di eseguire aggregazioni booleane (in modo analogo a `and` e `or`).
- L'elemento `ClaimsSchema` definisce l'attestazione da convalidare  e la regola `InputValidation` da usare per convalidare l'attestazione.

### <a name="defining-a-predicate-element"></a>Definizione di un elemento predicato

Ai predicati possono essere associati due tipi di metodo: IsLengthRange e MatchesRegex. Di seguito viene riportato un esempio per ciascuno dei due metodi.  Si esaminerà per primo un esempio del metodo MatchesRegex, usato per trovare una corrispondenza con un'espressione regolare.  In questo esempio, viene trovata una corrispondenza con una stringa contenente numeri.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Si esaminerà ora un esempio di IsLengthRange.  Questo metodo accetta una lunghezza minima e massima della stringa.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Usare l'attributo `HelpText` per inviare all'utente un messaggio di errore nel caso in cui il controllo abbia esito negativo.  Questa stringa può essere localizzata usando la [funzionalità di personalizzazione della lingua](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definizione di un elemento InputValidation

Un `InputValidation` è un'aggregazione di `PredicateReferences`. `InputValidation` può avere esito positivo solo se ogni elemento `PredicateReferences` restituisce il valore true.  È tuttavia possibile specificare quanti controlli `PredicateReference` devono restituire il valore true usando un attributo chiamato `MatchAtLeast` nell'elemento `PredicateReferences`.  È possibile anche definire un attributo `HelpText` per ignorare il messaggio di errore definito negli elementi `Predicate` a cui fa riferimento.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Definizione di un elemento ClaimsSchema

I tipi di attestazione `newPassword` e `reenterPassword` sono considerati speciali ed è quindi opportuno non modificarne i nomi.  L'interfaccia utente verifica che, durante la creazione dell'account, l'utente abbia correttamente reimmesso la password in base a questi elementi `ClaimType`.  Per trovare gli stessi elementi `ClaimType`, cercare TrustFrameworkBase.xml nel pacchetto Starter.  Il fattore di novità dell'esempio è che viene eseguito l'override di questi elementi per definire un `InputValidationReference` e l'attributo `ID` del nuovo elemento fa riferimento all'elemento `InputValidation` definito.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Riassumendo

Questo esempio illustra come vengano combinate le parti per formare criteri correttamente funzionanti.  Per usare questo esempio:

1. Seguire le istruzioni presenti nell'articolo obbligatorio [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md) per scaricare, configurare e caricare TrustFrameworkBase.xml e TrustFrameworkExtensions.xml
1. Creare un file SignUporSignIn.xml usando il contenuto dell'esempio riportato in questa sezione.
1. Aggiornare SignUporSignIn.xml sostituendo `yourtenant` con il nome del tenant di Azure AD B2C.
1. Caricare infine il file dei criteri SignUporSignIn.xml.

Questo esempio contiene una convalida per le password di soli numeri (PIN) e una per le password complesse:

- Cercare `PINpassword`. Questo elemento `InputValidation` convalida un PIN di qualsiasi lunghezza.  Al momento non viene usato poiché l'elemento `InputValidationReference` all'interno di `ClaimType` non fa riferimento ad esso. 
- Cercare `PasswordValidation`. Questo elemento `InputValidation` convalida una password compresa tra 8 e 16 caratteri e con 3 dei 4 tipi di carattere seguenti: lettere maiuscole, lettere minuscole, numeri o simboli.  Poiché `ClaimType` fa riferimento ad esso,  significa che nei criteri viene applicata questa regola.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
