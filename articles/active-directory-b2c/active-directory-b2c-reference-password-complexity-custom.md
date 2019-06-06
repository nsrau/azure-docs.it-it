---
title: Configurare la complessità delle password usando criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Come configurare i requisiti di complessità delle password usando criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 04a37e6faf51787457d7ca4ab8434fd253deb2ed
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509148"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare la complessità delle password usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory (Azure AD) B2C è possibile configurare i requisiti di complessità delle password specificate da un utente durante la creazione di un account. Per impostazione predefinita, Azure AD B2C usa password di complessità **Alta**. Questo articolo illustra come configurare la complessità delle password usando [criteri personalizzati](active-directory-b2c-overview-custom.md). È anche possibile configurare la complessità delle password usando [flussi utente](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Prerequisiti

Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Aggiungere gli elementi

1. Copiare il file *SignUpOrSignIn.xml* scaricato con il pacchetto starter e denominarlo *SingUpOrSignInPasswordComplexity.xml*.
2. Aprire il file *SingUpOrSignInPasswordComplexity.xml* e modificare **PolicyId** e **PublicPolicyUri** impostandoli su un nuovo nome dei criteri. Ad esempio, *B2C_1A_signup_signin_password_complexity*.
3. Aggiungere gli elementi **ClaimType** seguenti con gli identificatori `newPassword` e `reenterPassword`:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. Gli elementi [Predicates](predicates.md) hanno i tipi di metodi `IsLengthRange` o `MatchesRegex`. Il tipo `MatchesRegex` viene usato per associare un'espressione regolare. Il tipo `IsLengthRange` accetta una lunghezza minima e massima della stringa. Aggiungere un elemento **Predicates** all'elemento **BuildingBlocks**, se non esiste, con gli elementi **Predicate** seguenti:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Ciascun elemento **InputValidation** viene costruito usando gli elementi **Predicate** definiti. Questo elemento consente di eseguire aggregazioni booleane simili a `and` e `or`. Aggiungere un elemento **InputValidations** all'elemento **BuildingBlocks**, se non esiste, con l'elemento **InputValidation** seguente:

    ```XML
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
    </InputValidations>
    ```

6. Assicurarsi che il profilo tecnico **PolicyProfile** contenga gli elementi seguenti:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Salvare il file dei criteri.

## <a name="test-your-policy"></a>Verificare i criteri

Durante il test delle applicazioni in Azure AD B2C, può essere utile avere restituito il token di Azure AD B2C a `https://jwt.ms` per riuscire a esaminare le attestazioni in essa.

### <a name="upload-the-files"></a>Caricare i file

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Nella pagina dei criteri personalizzati, fare clic su **Carica criterio**.
6. Selezionare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *SingUpOrSignInPasswordComplexity.xml*.
7. Fare clic su **Carica**.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire il criterio che è stato modificato. Ad esempio, *B2C_1A_signup_signin_password_complexity*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui adesso**.
4. Selezionare **Iscriversi adesso** e immettere un indirizzo di posta elettronica e una nuova password. Vengono presentate linee guida relative alle restrizioni delle password. Immettere le informazioni utente e quindi fare clic su **Crea**. Viene visualizzato il contenuto del token restituito.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [Configurare la modifica delle password usando criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


