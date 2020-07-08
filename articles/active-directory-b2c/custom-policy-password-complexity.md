---
title: Configurare la complessità delle password usando criteri personalizzati
titleSuffix: Azure AD B2C
description: Come configurare i requisiti di complessità delle password usando criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389004"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare la complessità delle password usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C), è possibile configurare i requisiti di complessità per le password fornite da un utente durante la creazione di un account. Per impostazione predefinita, Azure AD B2C usa password di complessità **Alta**. Questo articolo illustra come configurare la complessità delle password usando [criteri personalizzati](custom-policy-overview.md). È anche possibile configurare la complessità delle password usando [flussi utente](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.


## <a name="add-the-elements"></a>Aggiungere gli elementi

Per configurare la complessità delle password, eseguire l'override dei `newPassword` `reenterPassword` [tipi di attestazione](claimsschema.md) e con un riferimento alle [convalide del predicato](predicates.md#predicatevalidations). L'elemento PredicateValidations raggruppa un set di predicati per formare una convalida dell'input dell'utente che può essere applicata a un tipo di attestazione. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le `newPassword` `reenterPassword` attestazioni e all'elemento **ClaimsSchema** .

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. I [predicati](predicates.md) definiscono una convalida di base per verificare il valore di un tipo di attestazione e restituisce true o false. La convalida viene eseguita utilizzando un elemento del metodo specificato e un set di parametri relativi al metodo. Aggiungere i predicati seguenti all'elemento **BuildingBlocks** immediatamente dopo la chiusura dell' `</ClaimsSchema>` elemento:

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
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
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Aggiungere le convalide del predicato seguenti all'elemento **BuildingBlocks** , immediatamente dopo la chiusura dell' `</Predicates>` elemento:

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

1. I profili tecnici seguenti sono [Active Directory profili tecnici](active-directory-technical-profile.md)che leggono e scrivono i dati Azure Active Directory. Eseguire l'override di questi profili tecnici nel file di estensione. Utilizzare `PersistedClaims` per disabilitare i criteri password complesse. Trovare l'elemento **ClaimsProviders**.  Aggiungere i seguenti provider di attestazioni come indicato di seguito:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Salvare il file dei criteri.

## <a name="test-your-policy"></a>Verificare i criteri

### <a name="upload-the-files"></a>Caricare i file

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Nella pagina dei criteri personalizzati, fare clic su **Carica criterio**.
6. Selezionare **Sovrascrivi il criterio se esistente**, quindi cercare e selezionare il file *TrustFrameworkExtensions.xml* .
7. Fare clic su **Carica**.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire i criteri di iscrizione o di accesso. Ad esempio, *B2C_1A_signup_signin*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui adesso**.
4. Selezionare **Iscriversi adesso** e immettere un indirizzo di posta elettronica e una nuova password. Vengono presentate linee guida relative alle restrizioni delle password. Immettere le informazioni utente e quindi fare clic su **Crea**. Viene visualizzato il contenuto del token restituito.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [Configurare la modifica delle password usando criteri personalizzati in Azure Active Directory B2C](custom-policy-password-change.md).
- Altre informazioni sui [predicati](predicates.md) e sugli elementi [PREDICATEVALIDATIONS](predicates.md#predicatevalidations) nel riferimento Framework dell'esperienza.
